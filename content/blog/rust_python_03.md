+++
title = "Rust in Python part 3: publish a Python package"
date = 2024-11-03

[taxonomies]
tags = ["Blog", "Rust", "Python"]
+++
In the previous entries in this series, the concept of moving functionality from Python to Rust was illustrated using
examples out of real-life application. On this page, I want to talk about my experience of doing a similar addition to
my `intervalues` package, which can be found [here](https://github.com/debruijn/intervalues). I will not fully discuss
the Rust implementation details, but just the overall learnings and suggestions.

## What is intervalues?
`intervalues` is a Python package aimed at processing valued intervals efficiently. With valued intervals, I mean 
something like "All values between 2 and 4, with a value of 3". Say you note this down as `[2-4; 3]`, and you have
any valued interval `[1-3; 2]`. Then you might want to add these together, which would produce the three valued
intervals `[1-2; 2]`, `[2-3; 5]` and `[3-4; 3]` that would summarize for each input the total value. Tasks like this
have popped up in some previous Advent of Code challenges, and I can imagine practical applications as well, with the
value part often being interpreted as "counts". For example, how many planes are there on an airfield at any given time
for a recurring daily or weekly schedule? In that case, each input interval would be a 1-count interval with bounds set
to the landing and departure time of the plane. For planes that land at the end of the schedule and depart at the
beginning, there would be two intervals for both parts. Then, all these intervals could be combined to find the overlaps
and counts across the schedule in an exact and efficient way, which can be used to identify potential bottlenecks or
expansion opportunities.

Although other interval-themed Python packages exist, the 'count' or 'value' perspective of it was not represented.
Instead, these other packages are often limited to looking at it from a Set perspective, so finding either the union or
the intersection of the input intervals. Translated to the airfield example above: "At what times is there at least
one plane on the airfield?" and "At what times are all scheduled planes on the airfield simultaneously?". These
questions can be useful as well ("When should the airfield be open?" for the first case for example) but could be 
answered by `intervalues` as well by looking at all intervals with a count >= 1 for the first case, and all intervals
with the count equal to the number of planes for the second case.

The package provides functionality for all kinds of data management-like tasks you might want to do with one or more
intervals: adding together, shifting, finding the most-common interval in an interval collection, finding the value of
a particular input, etc.
At the core of this, there is an algorithm for combining the intervals together, appropriately called 
`combine_intervals`. This algorithm is the key thing that could slow the package down in case of a huge dataset, and 
because of that it is the key candidate to consider for porting over to Rust for a potential speed boost.

## The Rust implementation
Implementing `combine_intervals` in Rust was a task that I tackled from the core up. An interesting challenge in this
was that the types of the numbers have to be defined beforehand as Rust is a typed language. First, I avoided that by
implementing the function just for `isize` input for both the bounds and the value, and then I made variations of it
where either could be `f32` as well, as proof of concepts. It was good to start with this, because a potential blocker
quickly became apparent: the `combine_intervals` algorithm requires the bounds to be used as index in a `HashMap` (the
Rust version of a `dict` in Python) but the standard floats in Rust are not hashable. So first, this challenge had to 
be solved, for which I ended up with two solutions:
- using `Decimals` from `rust_decimals`, which is an alternative numeric implementation for (among other) floats
- creating my own `intfloat` [package](https://crates.io/crates/intfloat/) and Struct, which is faster than Decimals but
  also less reliable for accuracy
    - the issue with the latter is in how numbers are stored: as 2 integers, a base integer and a power-of-10 integer. In
      the conversion of, say, 2 x 10^-4 back to a float there is a risk of a small error to be introduced, for example
      to get 0.00020000001 instead of 0.0002. Since the '4' above is an input on the Python side, you can simply round it
      again in Python to the same number of digits - but this might be a problem in a more generic use of `intfloat` so
      in that case using `Decimals` might be smart.

Then, as next steps I slowly generalized the functions by making use of the Numeric trait (if you don't know about
Rust traits yet: don't worry about it): first combining all integer inputs into one function call, and all float inputs
into another; and then generalizing that to end up with a single function that is reused across all potential input
types. It supports the above mentioned `Decimal` and `IntFloat` together with all default integer types.

At first, the combined result was just a `Vec` containing the relevant numeric values for each aggregated valued
subinterval. It simply made sense to also collect this in a well-defined Struct, and so also the Rust version of having
an IntervalCollection. Because of this, the Rust code is also available and usable by itself without Python on
[crates.io](https://crates.io/crates/intervalues/), although the main goal of the project remains the Python code.

## Speed comparison
The main motivation of both the Rust version of `intervalues` and of the `intfloat` package is speed, so we need to
actually verify that both provide a speed boost compared to the Python version of `intervalues` and the `Decimals`
struct respectively.

### Intfloat vs Decimal
For this comparison, I use the demo implementation of the `intervalues` Rust package in its `main.rs`. This runs 4
setups in which one million intervals are generated and combined, with its inputs being:
1. `i32` for both integer bounds and value -> runs in 38.1 ms
2. `i32` for integer bounds and `Decimal` for float value -> runs in 60.6 ms
3. `Decimal` for both float bounds and value -> runs in 143.6 ms
4. `IntFloat` for both float bounds and value -> runs in 68.4 ms

This shows that using `IntFloat` is roughly twice as slow as using `i32` but twice as fast as using `Decimal`, at least
for this setup. On the one hand, this shows the added value of the existence of `IntFloat`. On the other hand, in the 
rare situation you would need to combine one million intervals, having to wait 0.1s is not too bad, so `Decimal` is
still a good option even if it's the slowest.

### combine_intervals: Python vs Rust
For the secondary speed check, I had to make my Rust package available for Python again using maturin, and then
make a wrapper to use either the Python or the Rust implementation depending on some input flag. To test for speed,
I added an example script that runs both of them, for both integer and float inputs.

The results are (for one million intervals) as follows:
- integers: 1774 ms (Python) vs 900 ms (Rust)
- floats: 1976 ms (Python) vs 857 ms (Rust)

This pattern holds for smaller number of intervals as well: Rust is about as fast for integers and floats whereas Python
is about twice as slow for integers, and a bit extra slow for floats. Also note that this call of Rust via Python is
significantly slower than the 38.1ms or 68.4ms for the same setup in pure Rust above. Again the same two factors appear
like in the previous entry in this blog series: there is the extra overhead of code that is needed to get this
translation to work, and next to that there are a million intervals (so 3 million values) that needs to be transferred
from Python to Rust.

Together, this shows that for the Python package, the Rust implementation of the core functionality can result in a
good speedup, but that it is also worthwhile to maintain the pure Rust version of the package for cases where extra
speed is absolutely critical. Having it confirmed that all these efforts are worth it, now we move on to the next step:
including the Rust code in my published PyPI package as well.

## Building a Python package with Rust code
In order to build `intervalues` with the Rust code in it as well in the way I did it, there were a few things I had 
to do:
- Update my `pyproject.toml` to include `setuptools-rust`
- Add a rust folder to my Python repo with a Rust-to-Python wrapper in it
- Add a MANIFEST.in to make the build process aware of the above files

Let's go over them one by one!

### Updating pyproject.toml
To adjust the build process to make use of the Rust packages as well, we need to update the file that is the input to
the build process: `pyproject.toml` (of an alternative to that if you use it). In this toml, we need to update the
`build-system` section:
```toml
[build-system]
requires = ["setuptools>=61.0", "setuptools-rust"]
build-backend = "setuptools.build_meta"
```
Also, we need to add a new section specifically for `setuptools-rust`:
```toml
[[tool.setuptools-rust.ext-modules]]
# Private Rust extension module to be nested into the Python package
target = "intervalues_pyrust"   # Name of the package/module as defined in Cargo.toml
path = "rust/Cargo.toml"        # The location of the Cargo.toml
binding = "PyO3"                # Default value, can be omitted
```
You can see here already a peak of the name and location of the rust files in the next subsection. Next to this, you
can specify which binding to use, which is here defined as the default `PyO3` (which is that same technology as what
maturin uses).

Finally, an issue I encountered was that I specified my version in a `version.py`. For my setup, this will not work, 
since the python modules can't be imported before the rust-wrapper has been made available to Python. If you are in the
same situation, you have two basic choices: overhaul your code to delay the import of the rust wrapper, or instead put
your version information in a non-Python file. I have chosen the latter using the good old `VERSION` setup:
```toml
[tool.setuptools.dynamic]
version = {file = "VERSION"}
```

### Add a rust folder with the wrapper functionality
I created a very simple `rust` folder in the root of my Python repository containing (in essence) not much more than
a `Cargo.toml` and a `src/lib.rs` file with all its functionality. In the `lib.rs` file the two `combine_intervals`
functions for int and float input are defined, which both call the same `combine_intervals` from the actual Rust 
`intervalues` package but with different before steps (the ints are `isize`, whereas the floats are converted to
`IntFloat`).

This is confirmed by the associated `Cargo.toml` as well, which is setup using the same changes as needed for a Maturin
build: the `dependencies.pyo3` section with the specified ABI, the `crate_type` being set to `cdylib`, and the
`opt-level` set to 3. Next to that, it has dependencies set to the main `intervalues` and `intfloat` since it uses
them both (and also `itertools`). The name is specified as `intervalues_pyrust` in both the package and lib section
of the toml. 

Note that these two files are all that is technically needed (plus the automatically generated `Cargo.lock`), but I also
included a LICENSE and README.md file for publishing this `intervalues_pyrust` to crates.io as well, and a separate
`pyproject.toml` with all the Maturin-specific definitions in case I want to try out something quickly without fully
building the Python wheel.

Why all of this work for this separate wrapper with a new name? A couple of reasons:
- I wanted to split the Rust and Python work as much as possible in separate repositories.
- I also wanted to split the functionality of the pure Rust version and the wrapper, such that I can individually
maintain and update those
- With the default `intervalues` name for the rust package, there was some potential for conflict with the folder with
my Python code having the same name (and during the Maturin test phase, this really was an issue). Of course, I can also
just rename that folder without impacting the name of the package, but I prefer not to overhaul the Python part due to 
some non-Python reason.

I didn't have to publish this `intervalues_pyrust` on crates.io as well, but I felt like doing that anyway, at the very
least to claim that name.

### Add a MANIFEST.in file
Finally, the shortest addition is a MANIFEST.in file at the project root. This file will make sure the build process
can find and use the files in the rust folder. It is in my case defined as:
```
include rust/Cargo.toml
recursive-include rust/ *.rs
```

The result of all this is a buildable Python wheel that contains the Rust code. Before uploading to PyPI I did the basic
checks: can I install it locally on my system? Can I install it in a Docker image? In both cases, can I also use it to
run the Rust specific functions? All systems were go, so let's publish to PyPI using twine like I normally do. And then
it happens: the error that I can't upload my wheel because it is compiled specifically for my system. (Like the issue
described [here](https://stackoverflow.com/questions/59451069/binary-wheel-cant-be-uploaded-on-pypi-using-twine) on 
StackOverflow).

## Publishing a Python package with Rust code
So, the main lesson from the problem above: by compiling my Rust code, I can't share it in the same way I can share my
Python packages. Since "normal" `intervalues` is written in Pure Python, it can be made available in the broadest way
possible, with the only restriction due to me using some syntax introduced in Python 3.10. But now, in the way I have
build my Python&Rust combined `intervalues`, it can only be used on a Linux system setup similar to mine, and that is
not acceptable for PyPI.

So, how to solve this? Like mentioned on the StackOverflow link above, for this we can use the `manylinux` project and 
the `auditwheel` tool. 

This project has as goal to make tools available that will make it you can build a Python package with
compiled code and still make it available across "many linux setups" (hence the name). They do this by offering Docker
images that have been configured with compiler toolchains from some time ago, making sure that all recent Linux
distributions support the output of it. They offer multiple versions depending on how far back you want to go.

One of the other software in these Docker images is the `auditwheel` tool to be used to make sure the wheels that are
built indeed conform to all requirements needed for uploading to PyPI. 

In order to use this, download the `build-wheels.sh` file on 
[this link](https://setuptools-rust.readthedocs.io/en/latest/building_wheels.html) and adjust it to your project needs.
For `intervalues` you can see this in the [repository](https://github.com/debruijn/intervalues). My adjustments are
to target Python 3.10 and up, together with Pypy 3.10. Next to that, of course, I had to adjust the example project
name to `intervalues`.

Then, to use this with the Docker image, pull the one you want to use (e.g. 
`docker pull quay.io/pypa/manylinux2014_x86_64`) and from the project root run the script above using this Docker image
with the following command: 
```
docker run --rm -v `pwd`:/io quay.io/pypa/manylinux2014_x86_64 bash /io/build-wheels.sh
```

Note that this will take more time than a normal Python package build, because it will do the build and checks for all
specified versions of CPython and PyPy. The result is also a set of wheels for each of these, instead of a single one
for Python in general. These need to all be uploaded to PyPI to make available for installation. 

But then after all this.. it works! I can `pip install` the new version of `intervalues` and make use of the Rust
functionality in both Python 3.10 and PyPy 3.10. The only downside is that it is now (afaik) Linux specific: this newest
version (as of writing) can't be installed on Windows whereas the version before can. I can't verify this since I don't
have a Windows box available right now, but this is what I have been able to gather from what I read about it. To be
honest, I personally don't really care about that. But if there is an `intervalues` user who wants me to find out if/how
it can still be used on Windows even with this Rust functionality, let me know (or, of course, submit a PR yourself).
Alternatively, the old version still works on Windows and there are no real changes from that version to this one
outside of the Rust wrapper.

## Alternative setups
There are several alternatives to consider to some of the steps that I have taken, which I might experiment with in the
future. Experiences from readers are also welcome, especially if these alternatives work really good or really bad.

- Maturin allows you to directly publish the Python package with Rust functionality using `maturin publish` instead of
`maturin develop`. I have not tried this, so I don't know whether the result is OS-agnostic or if it uses some kind of
`manylinux` restriction as well, or something else. Also, this feels like it would make most sense if the wrapper was
included in the base Rust `intervalues` package, since it will introduce another package itself (a Python package that
the main Python `intervalues` package can include as a dependency). 
- I want to look into how to make the Rust component optional, and if that makes the core Python functionality still
usable on Windows.
  - I could (either by using Maturin as above, or manually) publish the Python side of the Python/Rust layer separately
  from the rest of the Python code, and make it an optional dependency of the main `intervalues`. 
  - Perhaps there also other ways of doing this
- Ideally, I want to have the Rust setup also usable on Windows. Like discussed in the previous section, I will likely
not look into this myself until a Windows user requests this, especially if I can figure out how to create non-Rust
future versions as well that are more universally usable.
  - Probably the first candidate to explore for this functionality is to use the `cibuildwheel` as mentioned in the 
    [setuptools-rust](https://setuptools-rust.readthedocs.io/en/latest/building_wheels.html) docs as well.
- Let me know if you have other suggestions as well for how to best set this up!

Depending on these investigations and/or suggestions, there might be a future entry in this series to discuss them. But
for now, the next (and potentially final) entry in this series will be on my 
[overall learnings and experience](/blog/rust-python-04) of using Rust in Python. Alternatively, have a look at some of the
links down below for further reading.

## Links

- Some of the other Python interval packages:
  - portion
  - pyinterval
- My repo's and packages related to intervals:
  - Python intervalues: [github](https://github.com/debruijn/intervalues) and [PyPI](https://pypi.org/project/intervalues/)
  - Rust intervalues: [github](https://github.com/debruijn/intervalues_rust) and [crates.io](https://crates.io/crates/intervalues/)
  - intervalues_pyrust: [crates.io](https://crates.io/crates/intervalues_pyrust)
  - intfloat: [github](https://github.com/debruijn/intfloat) and [crates.io](https://crates.io/crates/intfloat/)
- More resources on setuptools-rust:
  - [Overall doc on setuptools-rust](https://setuptools-rust.readthedocs.io/en/latest/README.html)
  - [If you want to use setup.py instead of pyproject.toml](https://setuptools-rust.readthedocs.io/en/latest/setuppy_tutorial.html)
  - [Building wheels](https://setuptools-rust.readthedocs.io/en/latest/building_wheels.html)
- More resources on manylinux and auditwheel:
  - [The manylinux repository](https://github.com/pypa/manylinux) with details on what the different `manylinux`
    versions imply for Python and distribution support.
  - [The auditwheel tool](https://github.com/pypa/auditwheel) showing examples of other commands that can be supplied
    to it, for example for only checking (not repairing) existing wheels for PyPI compatibility.
