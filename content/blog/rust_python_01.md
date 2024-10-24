+++
title = "Rust in Python part 1: getting started"
date = 2024-10-19

[taxonomies]
tags = ["Blog", "Rust", "Python"]
+++

On this page, I will talk you through how to get started with using Rust in Python. For this, I will assume you know the
basics of Python (defining a function, importing a package, etc). To add on that, this page will present:
- Rust, & how to get started with that up to a sufficient level for the goal of a single Python function call
- An example Python project to instead run via Rust
- The same functionality written in Rust
- How to compile and expose Rust code for use in Python
- How to adjust Python code to use this Rust code
- Comparison of run time
- More links and references

The goal of this is to present a single solution that you can just copy to get started. Later on I will introduce some
choices and alternatives that you might consider, but I want you to get to the power Rust from Python as fast as
possible. So let's dive in!

## Getting started with Rust
If you are 100% new to Rust (like I was a couple of weeks ago), there are two things you need to do to get started up
to the level needed for this blog series: install Rust, and learn some basic Rust. For both of these, I suggest to
follow "The Book" of Rust [here](https://doc.rust-lang.org/book/title-page.html).

To install Rust, follow instructions on [section 1.1](https://doc.rust-lang.org/book/ch01-01-installation.html) of the
book. In case you are using Linux like I do, that amounts to running:
```commandline
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```
This downloads a shell script that installs the `rustup` tool which is used to maintain versions of Rust. More details
can be found on the linked page.

The next step is to "just learn Rust". But don't feel that this is too big of a hurdle! As mentioned on the introduction
page, you don't need to know all concepts of the Rust language to get to a stage where you can already replace a
specific Python function with Rust calculations - often the ones where this matter the most can be written using the
most basic components of the language. More complicated stuff will only be relevant when more of your codebase will be
written in Rust, which will come later together with learning those details later.

So to get that basic level of Rust as needed for this series, continue reading "The Book" up to (and including) 
chapter 4. This means you will learn about how to compile and run a piece of Rust code, how to work with variables,
functions, if-statements, for-loops and comments, which are all things that should be familiar to you from the Python
world, although some things in Rust are defined stricter. Next to that, you will be introduced to a thing that almost
uniquely defines Rust: its ownership rules and borrowing system.

Then, you can come back here. Or, you can continue on reading about Rust. If you do, you will learn more about how Rust
works which allows you to write more advanced stand-alone or library code. I can suggest combining that with doing the
[Rustlings](https://github.com/rust-lang/rustlings) exercises, which mostly follow along with The Book.

In any case, at some point I hope you will be back here, either with some basic or more advanced knowledge of Rust. (If
you decide to just read along without knowing Rust, I think you will be able to understand most concepts as well, 
although some terms might not make sense to you).

## Example project
On this page, we will show an example project in Python, that we will adjust to make use of calculations in Rust
instead. In this, for instructive purposes, suppose we want to calculate the nth number in the  
[Fibonacci](https://en.wikipedia.org/wiki/Fibonacci_sequence) sequence, defined by the sum of the previous two values
with 0 and 1 (or 1 and 1) as first two values. We will do this in a stupid way in both Python and Rust, which will help
to demonstrate the speed gain without being [smart in the first way](../rust-python-00).

### Initialize the project
To start out, first go into the folder in which you put your projects and run `cargo new rust_in_python`. This will 
create a folder of that name with in it a few Rust related files that we will adjust along the way. But first, let's 
make sure our Python setup that we will aim to replace is there.

Note: in this setup we will make use of Maturin for building our Python packages with Rust compute. Make sure in your
Python setup you have `maturin` and `patchelf` available, either by running `pip install maturin[patchelf]` or 
separately installing both packages. I recommend installing these not for your system python interpreter, but using
a virtual environment or something similar (which is outside the scope of this series, but if you are new to it, you
can read up on it [here](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)).

### Python implementation
The function that we will use to calculate the nth entry in the Fibonacci sequence in an inefficient ("stupid") way is:
```python
def fibo(n):
    if n == 1:
        return 1
    if n == 2:
        return 1
    return fibo(n-2) + fibo(n-1)
```
Now I can talk about what the stupid approach is: this approach will recalculate the same fibonacci number multiple
times. The number at `n-1` is calculated 1, but the number at `n-2` is calculated twice: once for calculating `n-1`, and
once in the final sum for `n`. It only gets worse from here: `n-3` is calculated three times (two times for `n-2`, and
then once more for `n-1`), `n-4` five times, `n-5` eight times, et cetera. (Do you see the pattern in here?)

A more efficient calculation would store the already-calculated values in some way or avoid having to do the calculation
multiple times in another way. But remember: we are not trying to be type-1 smart here. So, we use the calculation above
to calculate, say, the 40th entry in the sequence:

```python
import time
before = time.time()
result = fibo(40)
after = time.time()
print(f"Result in Python after {after-before:.2f} seconds is: {result}.")
```
which prints on my PC
```
Result in Python after 19.07 seconds is: 102334155.
```

Let's see what this looks like in Rust!

### Rust implementation

The analogue function definition in Rust could be written like:
```rust
pub fn fibo(n: usize) -> usize {
    if n == 1 || n == 2 {
        1
    } else {
        fibo(n - 2) + fibo(n - 1)
    }
}
```
Note that if you got further than chapter 4 in the Book of Rust, you will have been introduced to the `match` syntax
which is an alternative and possibly more "rustonic"; but this version works as well and contains no Rust syntax
that is not introduced in the first 4 chapters.

To run the above as pure Rust code, you could put the above directly in `main.rs` in your `src/` folder. The result 
could be like what follows, which also includes `Instant` from the standard Rust library to create a simple timer:
```rust
use std::time::Instant;

pub fn fibo(n: usize) -> usize {
    if n == 1 || n == 2 {
        1
    } else {
        fibo(n - 2) + fibo(n - 1)
    }
}

fn main() {
    let before = Instant::now();
    let res = fibo(40);
    let after = Instant::now();
    println!("Result in Rust after {:?} is: {}.", after - before, res)
    }
```
On my PC executing this with `cargo run` results in the output:
```
Result in Rust after 650.475884ms is: 102334155.
```
Two important observations:
1. Happy to see the answer is the same! Otherwise we'd have a problem..
2. Also, happy to see that Rust is much faster! Otherwise, we'd be doing this without gain.

### Rust adjustments for use in Python
To convert the above to something that can be used in Python, some things have to be changed:
- Move the `fibo` function above to `lib.rs` in the `src/` folder
- In `main.rs`, add `use fibo_rust::fibo;` to the top
- In `lib.rs`, adjust `fibo` to be a pyfunction (see below)
- Also add `fiborust` as a pymodule (also see below)
- Update the `Cargo.toml` to make use of pyo3 (see even further below)

The resulting `lib.rs` becomes:
```rust
use pyo3::prelude::*;

#[pyfunction]
pub fn fibo(n: usize) -> usize {
    if n == 1 || n == 2 {
        1
    } else {
        fibo(n - 2) + fibo(n - 1)
    }
}

#[pymodule]
#[pyo3(name = "fibo_rust")]
pub fn fiborust(m: &Bound<'_, PyModule>) -> PyResult<()> {
    m.add_function(wrap_pyfunction!(fibo, m)?)?;
    Ok(())
}
```
In this, the name `fibo_rust` is used to make clear what you will import from in Python. What additionally is important
to note:
- we are importing from `pyo3` on top, which is the package Maturin uses behind the scenes for the Rust-Python
integration
- each function you want to use in Python needs to be flagged with `#[pyfunction]`. This will (among other things) make
sure the Rust compiler will check whether this Rust variable can be converted to a Python variable (which is fine for
builtin variable types like `usize`)
- each function you want to use in Python also needs to be included in the `#[pymodule]` section using the 
`add_function` call. The other syntax used in the `fiborust` definition is outside the scope of this blog. 

The updated `Cargo.toml` will have to contain the following lines:
```toml
[lib]
name = "fibo_rust"
crate-type = ["cdylib", "lib"]

[dependencies.pyo3]
version = "0.22.5"
features = ["abi3-py310"]
```
This specifies:
- the name the library will have in Python (as a module) - I can recommend something different than the folder name
- the library-type, which next to normal `lib` now also needs to be `cdylib` (you can also leave out `lib` but then
the result is no longer runnable in Rust by itself using `cargo run`; in that case, you can remove `main.rs`)
- the version of pyo3 to use
- which Python ABI to use as a lowerbound - I have selected Python 3.10 here but other values can be used as well
(up from py38 for Python 3.8 as the lowest)

### Compiling the Rust package for Python use
Next, we will finally be using Maturin for actually making the Rust function available to be called in Python. From
the same directory as that contains your `src` folder and your `Cargo.toml`, run the command `maturin develop` (with
your virtual environment active, if you are choosing to make use of that setup). This will automatically compile the
Rust code, wrap it with some Python-to-Rust and Rust-to-Python variable converters, and put it in your `site-packages`
in the currently used Python interpreter. That means it is importable from Python!

### Adjustments in Python code
To import and use the package in Python, adjust the above Python script to the following:

```python
from fibo_rust import fibo
import time

before = time.time()
result = fibo(40)
after = time.time()
print(f"Result in Python with Rust after {after-before:.2f} seconds is: {result}.")
```
Note that this is the same code as above, with just the `fibo` function definition replaced with importing `fibo` from
the `fibo_rust` module. As a Python user, nothing would make it seem that this module is not just a native Python
function. Except for the second note, namely that your IDE might not (immediately) recognize this new module and flag 
it as an import that shouldn't work, even though it does work. Your mileage may vary.

On my computer, running the above code prints:
```
Result in Python with Rust after 0.67 seconds is: 102334155.
```

It works! The runtime of 0.67 seconds is roughly comparable with the 650ms reported by Rust, and significantly lower
than the initial 19.07 seconds from the first Python version. For this, we needed only very basic Rust knowledge, 
a very simple reimplementation of the calculation in Rust without changing the internal logic, and the setup with
Maturin and the adjustments in `Cargo.toml`. Not too much effort for around a 30x speed boost. Great!

At least.. as long as this actually is a realistic representation of what you might achieve in practice. Is it? 
It can be, but by how much can differ. Let's look at a different example that illustrates a potential limitation in
[the next part](../rust-python-02), or check out some additional background and reading below first.

## Links
For full code used in this example, have a look at the `rust_in_python` folder in my 
[websites_examples](https://github.com/debruijn/website_examples) repository.

In case you'd like to view some other examples of using Maturin, have a look at these links:
- On the main website of maturin, there is a different [tutorial](https://www.maturin.rs/tutorial) to get you started. 
This one uses a bit more advanced Rust syntax, but can be helpful if you are also a bit further in your Rust journey.
- Some well-known Python packages use Rust and Maturin under the hood, like [polars](https://github.com/pola-rs/polars)
and [ruff](https://github.com/astral-sh/ruff).

