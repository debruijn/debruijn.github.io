+++
title = "Rust in Python: summary of learnings so far"
date = 2024-11-04
draft = false

[taxonomies]
categories = ["blog"]
tags = ["rust", "python"]

[extra]
lang = "en"
toc = true
comment = true
copy = true
math = false
mermaid = false
outdate_alert = true
outdate_alert_days = 120
display_tags = true
truncate_summary = false
featured = false
reaction = false
+++


In the [past](/posts/rust-python-01) [few](/posts/rust-python-02) [entries](/posts/rust-python-03) in this blog series, I have 
presented my approach to incorporate Rust in Python projects. So, what are my current views about learning Rust simply 
to speed up some critical functions? Would I do it again if I had to start from scratch now? And where do I see the 
practical use for it in day to day work?

### Not as big of an effort as you might think
Learning Rust to a point where it is enough to do this at all does not take a lot of time. Of course, you can learn
way more Rust, but the gains can already be achieved quite quickly. The key help in that is the Rust compiler, being
nitpicky (in a good way) to limit the issues you might have when you just want it to work reliably.

### I like Rusts tooling and existing packages
Next to the nitpicky compiler, I like the rest of the common Rust tools: so far, `cargo` seems very smooth to me, and I
like `crates.io` although it helps I was already familiar with PyPI. A lot of features that I would use to solve 
particular issues in Python have analogue packages in Rust (where that makes sense), like `itertools` for efficiently
iterating in various ways. So you don't have to reimplement logic if your brain thinks `pairwise` or `combinations`.

### I enjoy Rust
I actually enjoy using it so far. Next to going through the entire Book and the rustlings project, I have also applied
it to about half a year of Advent of Code challenges (with and without Python integration!), and I have also set up a
Rust runner for the upcoming Advent of SQL event (here are the [event website](https://www.adventofsql.com/) and 
[my repo](https://github.com/debruijn/adventofsql) for it). Even though for that latter Rust will just act as a 
potential SQL runner, it will be interesting to see how that compares with my Python implementation for the bigger
challenges later on in the month.

### Speed impact can be big
The impact on the speed can be big. In the previous blog posts I have shown speed increases up to a factor of 1000
compared to my Python implementations (in which the quality of the Python implementation is a factor as well of course),
and in general in doing more Advent of Code challenges in both Rust and Python, I get about a times 10 increase in most
cases. Of course, it will depend on the specifics. The gain will be lower if the Python alternative is already written
in C or Rust itself, and also if you have to transfer/convert a lot of data compared to the coding effort. In the latter
case, it might be worth it to find another spot for the switch to Rust, potentially even the entire task, to avoid any
Python-Rust communication delay.

### Keep limitations in mind
It is more work, not just to write, but also to maintain, extend, test, etc. Each time you will have to build, you will
have to wait a bit, and that might add up over time. There will be new packages to explore if you want to slowly extend
the Rust component in the project, which also takes time.

A potential big limitation depending on your work setup is the creation of multiplatform wheels, although I still have 
to test the `ci_buildwheel` tool that in theory would overcome all those issues.

### Type of projects and parts where this might work best
A list of project properties that could make using this easier:
- There is a clear core function that is used a lot but takes a lot of time, for which a potential time gain would
matter. This function is rarely updated since it just is what it is, but also has no Python implementation in C or Rust
available already.
- This core function is already tested well with unit tests that will immediately also test the Rust implementation.
- You use this internally in a controlled environment, such as in a project-specific Docker image - this immediately
skips most of the wheel issues.
- Alternatively, you use this as part of a package that has only a few maintainers that need to coordinate on this, and
you can get the multiplatform (or the optional part) to work.
- There is not already an expertise available in your team for implementing this functionality in C or other
alternatives instead. Of course, Rust can then still be an option as well.

### Personal plan of approach
So how do I plan to incorporate this into my coding work in the future? Some thoughts:
- To maintain my Rust skill, I plan on regularly doing some coding challenge in Rust. This might be next to another
primary implementation or it might the only one. For example, I plan on doing the upcoming Advent of Code 2024 in 
Python primarily, but secondarily in Rust as well, especially for days where I am curious on how that would work or
how much that would matter.
- In my other side projects, I will stay on the lookout for functions that are noticably slow to see if I can speed
them up with Rust. In the case of `intervalues` in [blog post 3](/posts/rust-python-03) I simply really want to try it for
the experience; otherwise I don't know if I would have explored it since I don't know if it is very likely the practical
use cases of the package would involve such large datasets. But for other future side projects, who knows?
- In my work as a Data Scientist / Econometrician, Rust does not have a direct use for the key modeling components,
since either they are models for which run-time is not a factor (say, linear regressions or principal component
analyses), or there are dedicated Python packages that efficiently run this in some other optimized framework
(Tensorflow, JAX, Torch, etc; for stuff like neural networks and Bayesian MCMC models).
- That doesn't mean there can't be a step in the data processing or postmodeling for which Rust can't be used. In one
of my previous roles I worked on a product that had both a key Bayesian model and a key data matching step that would
generate input for that model. The latter is something I now think would gain a lot from being implemented in Rust,
especially since regularly there would be projects for which this step would be computationally more involved than the
actual model step.
- In future endeavours I hope these situations will present themselves. I just have to make sure I keep this option in
the back of my mind, and make sure I don't get rusty in Rust.
