+++
title = "Rust in Python: how to get started"
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
- Further discussion, links and references

So let's dive in!

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
    match n {
        1 => 1,
        2 => 1,
        _ => fibo(n - 2) + fibo(n - 1)
    }
}
```

Rust in Python: how to get started
- Install (and learn) Rust
  - Install: link to page, describe experiences - if any
  - Learn Rust: link to the book and Rustlings, describe experiences and personal order of doing this
- Example Python project
  - Take an average: my_average
- Same code in Rust
  - Take an average: my_average
  - Extra bits and pieces to make it usable in Python
- Commands to compile
  - Maturin etc
- Adjustments in Python code and comparison of results and runtime
  - Using simple time statement
- Links to other basic examples
