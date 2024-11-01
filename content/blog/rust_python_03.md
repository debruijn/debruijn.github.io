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

### What is intervalues?
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
intervals: adding together, shifting, finding the most-common interval, finding the value of a particular input, etc.
At the core of this, there is an algorithm for combining the intervals together, appropriately called 
`combine_intervals`. This algorithm is the key thing that could slow the package down in case of a huge dataset, and 
because of that it is the key candidate to consider for porting over to Rust for a potential speed boost.

### The Rust implementation
- At first: just that core function
- Expanding to a full standalone Rust package -> repo
- Difference between integers and floats
  - At first, separate functions
  - Refactored to automatically detecting what is needing based on input from Python
  - Created Intfloat for HashMap ("Dict") functionality
  - Also supports Decimal
- Speed comparison
  - There is a speed boost
  - Within Rust: Intfloat is faster than Decimal
  - Compared to Python: Rust is faster than pure Python and Pypy
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
