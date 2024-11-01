+++
title = "Rust in Python part 3: publish a Python package"
date = 2024-10-31

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



- Publishing the Python package
  - How to change your tomls, project structure, and using setuptools-rust
  - Warning: stuck on version file
  - First version: not PyPI compatible due to manylinux restriction
  - Use dedicated docker image for that with older GCC compiler to produce manylinux wheels
  - Now: limited to Linux only?
- Alternative setups to consider:
  - Directly publishing with maturin publish and keep as a separate package (dependency of core intervalues)
  - Provide an install without Rust as well, for Windows and older Python
  - Don't use the intervalues-pyrust layer -> related to point 1?
  - Let me know if you have other suggestions or experiences
- Next, and for now final, section: learnings across this entire endeavour
- Links:
  - Other interval packages in Python
  - My repo's and packages on intervals
  - Setuptools-rust docs
