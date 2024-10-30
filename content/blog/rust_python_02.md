+++
title = "Rust in Python part 2: more detailed example"
date = 2024-10-22

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
function. That makes this a good situation for potential gains by replacing this function with a Rust implementation.
But let's first have a look at the Python code we start out with..

### Initial Python implementation
### Doing the core calculations in Rust 
### Run-time comparison, incl a full Rust implementation
### Insights - smaller gain than expected due to communication step
### Additional tweaks (or just compilation flags if no other pops up + not mentioned on previous page since it didn't work there)
### Links
- Link to the code in my advent-of-code repo
- Details on compilation flags
