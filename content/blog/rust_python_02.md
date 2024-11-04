+++
title = "Rust in Python part 2: more detailed example"
date = 2024-10-30

[taxonomies]
tags = ["Blog", "Rust", "Python"]
+++

After the instructions on setting up Maturin for the "simple" Fibonacci example, you might have questions how well this
generalizes to bigger actual problems. Of course, the answer will be "it depends", and to get some more intuition on 
what it might depend on, let's first look into a bigger example that is still not an actual problem: day 5 of Advent
of Code 2018.

### What is Advent of Code?
First, in case you don't know: what is [Advent of Code](www.adventofcode.com)? It is a yearly fun coding event in which 
every day gives a new two-part programming puzzle to solve for all days from December 1st to 25th. Sometimes the 
problems themselves are already the challenge to solve (like finding a good algorithm with the right assumptions - 
that would be mostly language-agnostic). But other days, especially in the beginning, can be easy or difficult 
depending on what restrictions you put on yourself. Throughout my participation, I had several phases of my focus:
- "Pure Python" focus, after years of mostly working in pandas and numpy for these kind of steps
- Integrating more and more of itertools, functools and collections into my solutions
- Getting all the backlog of old years (before I joined) done
- Converting some of these puzzles to pure Rust or Rust-in-Python versions

You can see that there can be plenty of ways to approach this, depending on what you want to improve. Do you want to
write very clean code to make sure you are used to that for work-related coding tasks? Feel free to do that. Do you
instead want to write the most unreadable, undocumented code ever, simply because you can? Sure, go ahead. I would set
a goal and/or some restrictions in order to learn or develop some skills, but don't make it to hard since it should 
also still be something you can find fun.

### So.. what is the problem on day 5 of 2018?
Of course, if you plan on still doing the 2018 run of the event, this page will contain spoilers. I don't think this
matters much for day 5, but reading on is your own choice.

[Day 5](www.adventofcode.com/2018/day/5) deals with a fictional version of polymerization, which is represented here by
a long string of letters being reduced by removing subsequent lowercase and uppercase letters of the same type (so 
'Aa' or 'rR'). After removing one such combination, the surrounding letters might form a new pair that can be removed.
At some point, no letters remain that are immediately next to the same letter but in the opposite case, and the length
of that result gives the answer... for part a.

Like I said in the introduction of Advent of Code, each puzzle consists of two parts, and part b is often similar to
part a but with a twist that makes it more complicated, exponentially bigger, or something else. In this case: what is
the shortest final reduced string length you get if in the starting string all letters (upper and lowercase) of a single
letter of the alfabet was removed. For example, all p's and P's would be removed before the start of the procedure
above. Out of all possible letters to remove, the one that seemed to be the biggest blocker is the one that will lead
to the answer for this part.

This basically means that the same 'polymerization' step needs to be done 27 times: once in part a, and 26 times
in part b, once for each letter in the alfabet. Next to that, the majority of the compute time is in the call to that
function. Also, as I will show in the next section, the Python code was not really efficient (from a run-time
perspective) which means that significant gains can be made. That makes this a good situation for potential gains by 
replacing this function with a Rust implementation. But let's first have a look at the Python code we start out with..

### Initial Python implementation
The initial Python implementation for the polymerization step was programmed just in the context of the Advent of Code
event. Especially for the initial days of a year, my personal goal is to just get through them. In most cases I will
have a vague side goal of having decent performance, but in this particular case I skipped that since running this
using Python 3.10 takes about 9 seconds (and using PyPy takes about 7.5 seconds). For whatever reason, I chose to not
speed this up, even though I can see things that I could have done. This makes this a good candidate for replacing this
part of the code with a Rust implementation.

So what was my initial Python implementation?
```python
def run_polymerization(polymer):
    stop = False
    curr_len = len(polymer)
    while not stop:
        for pair in pairwise(polymer):
            if ord(pair[0]) - ord(pair[1]) in [32, -32]:
                polymer = polymer.replace(pair[0]+pair[1], "")
        if len(polymer) == curr_len:
            stop = True
        curr_len = len(polymer)
    return curr_len
```
Some notes:
- The variable `polymer` contains the string we try to collapse.
- `pairwise` refers to `itertools.pairwise` which loops over a iterable by presenting two subsequent elements in a 
    tuple, e.g. pairwise("abcd") -> ('a', b'), ('b', 'c'), ('c', 'd')
- To check if there is a pair, I converted the characters to their Unicode representation using `ord(.)`. The difference
    between an uppercase and a lowercase letter is 32.
  - Note that this implicitly uses the assumption that the string only contains letters, otherwise there would be other
    Unicode representations that can differ 32 from a letter. Example: ord('A') = 65 and ord('a') = 97, but ord('!') = 
    33, so not just the pair 'Aa' would be detected but also the pair 'A!' would be removed.
- I iterate on this in the while loop until there are no changes anymore, and then the length of the remaining polymer
  is returned.

Fairly straightforward, but also some optimizations could be done. But we are not here to be smart, at least not in that
way. Let's see what we can gain by reimplementing in Rust!


### Doing the core calculations in Rust 
Reimplementing this in Rust, I initially came up with this:
```rust
#[pyfunction]
pub fn run_polymerization_u8<'a>(input: Vec<u8>) -> usize {
    let mut v = Vec::new();
    for &c in input.iter() {
        match v.last() {
            None => v.push(c),
            Some(&d) => {
                if d.to_ascii_lowercase() == c.to_ascii_lowercase() && d != c {
                    v.pop();
                } else {
                    v.push(c);
                }
            }
        }
    }
    v.len()
}
```
This follows a different approach than in Python since I did not know of a good `pairwise` alternative:
- I build up the vector (e.g. the polymer) that would result after the process in variable `v`.
- For each entry that I get from the input, I compare it to the last entry in `v` to see if it would collapse
- The collapse I do with a `match` statement: 
  - if there is a `v.last()` and converted to lowercase it is the same but otherwise not (e.g. one is lowercase and the
  other is uppercase of the same letter), then that last element of `v` is removed (popped)
  - otherwise (so either there is no `v.last()` or this one is a different letter or the same letter in the same case),
  then instead add (push) the new element to `v`.
- When all elements in input are processed, `v` is stable and I return its length.

Would this implementation in Python be faster as well? Probably, but not to the extend that this one is (see next
section). Alternatively, a keen eye might have spotted that the input is already converted to u8 (e.g. 'encoded' in 
Python), which I have later on tested to confirm that it has no noticable impact on runtime.

### Run-time comparison, incl a full Rust implementation
The above Rust-function is made accessible with a `wrap_pyfunction` statement and can be imported in Python (see 
`aoc_2018/aoc_rust_2018/src/lib.rs` in my [advent of code](https://github.com/debruijn/adventofcode) repository for 
details on that). Then, after adjusting my Python function (`aoc_2018/aoc_5.py` in the above repository) to alternatively
make use of that version of `run_polymerization`, the runtimes can be compared:
- Pure Python: 9.07s
- PyPy: 7.55s
- Rust-in-Python: 0.09s
- Pure Rust: 0.0082s

This also includes a pure Rust implementation, which is out of scope for these series of blog posts, but for which the
details can also be seen in the above repository. Some insights from this example:
- There is a massive gain from going from pure Python to using Rust for the calculations
- There is another massive gain from using Rust for everything. This is due to 2 reasons:
  - My Python wrapper that I use for orchestrating my function executions does more unrelated stuff in its importing and
  printing of the results than the Rust wrapper.
  - Passing the 50000 character String over from Python to Rust is not for free

That last point is an important factor to keep in mind in determing the best spot for passing the work over to Rust:
if your implementation requires a lot of copy-overhead, it might be slower than if you put it slightly before or after
that point and you only pass over a small digit. In this case: it is faster to let Rust read in the file than to let
Python read in the file and pass it over, because then you are sort-of reading it in twice. How much this matters in
practice depends on details.

### Additional tweaks (or just compilation flags if no other pops up + not mentioned on previous page since it didn't work there)
Without much effort, there is another speed increase that can be gained in Rust by setting the compilation flag to 3 in 
your `Cargo.toml`:
```toml
[profile.dev]
opt-level = 3

[profile.release]
opt-level = 3
```
The compilation optimization level determines how much effort the compiler will put into trying to optimize the result
for execution speed. By default, the dev flag is 0 while the release flag is 3, but you can set it using the setup
above to any value from 0 to 3. Often, for more complicated projects, a level of 2 or 3 can be significantly faster 
than a level of 0. Whether 2 or 3 is the fastest can depend on details (the compiler can try to "overoptimize" on level
3), so it's good to try both when performance is critical.

After applying level 3 to the comparison above:
- Rust-in-Python: 0.01s instead of 0.09s
- Pure Rust: 0.0081s instead of 0.0082s

In this particular case, there is almost no gain in the pure Rust implementation, but in the Rust-in-Python one there is
a very significant increase bringing it much closer to the pure Rust version. Apparently, in the Maturin or Py03 wrapper
there is some code that the compiler can optimize well.

Note that for the Fibonacci example in the previous page the compiler flag does not matter at all, even for the 
Rust-in-Python version: in that particular case, the code is already so straightforward that the level-0 compilation
is already optimal. This shows that the complexity of the code can be a factor in how much the compiler can gain by
this extra level of optimization. So how does that show itself when looking at the performance of an actual Python
package? Let's have a look at the [next entry](/blog/rust-python-03) in this series, or have a look at some of the links 
below for further background reading.

### Links
- The [Advent of Code](www.adventofcode.com) website
- My Advent of Code [personal repository](https://github.com/debruijn/adventofcode)
- The section in the Rust book that discusses compiler flags in more detail is 
[section 14.1](https://doc.rust-lang.org/book/ch14-01-release-profiles.html)
