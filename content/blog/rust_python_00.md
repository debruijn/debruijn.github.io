+++
title = "Rust in Python part 0: why and how?"
date = 2024-10-18

[taxonomies]
tags = ["Blog", "Rust", "Python"]
+++

When coding in Python, you will occasionally be in a situation where a single function or a few functions are
responsible for the majority of your execution time. In most cases, that is fine: if for a particular task, 95% of your
time is spent in a single function but the total run time is 1 second, and this particular task is not ran frequently,
then there is no reason to worry about speed improvements.

But of course, the opposite will happen as well: this 1 second task needs to be ran 1000 or 1000000 times each time
you do a particular broader task (say, a model run in your data science research; or a ingestion of data in your 
pipeline; or every time a page loads on your website). If this becomes too critical to ignore, there are two things you
can do:
- Be smart
- Be smart, but differently

One way of being smart is looking at your process, and figure out if there are ways to avoid some of these calculations:
- Are you recalculating a variable in a loop that could be precalculated once?
- Can you use a cache to store the output for particular input states? And if so, can you be smarter about defining what
a state is, say when a permutation of the same inputs doesn't matter for the output value?
- Is there a faster implementation of what you try to do out there that you can use? Does that meet your requirements?
- Can you make some assumptions to skip over some of the calculations, and get to, say, 99% of the result in 1% of the
time?

This way of being smart requires you to think, which sometimes you don't want to do. You might also have to convince
other people that this is actually worth it. And: there is no guarantee that refactoring in such a way will lead to a
speed improvement.

So, what is that other way of being smart? Of course, given the title of this series of blog posts, the answer involves
implementing your Python functionality in Rust, and using that in Python. Ideally, from a Python user perspective, they
wouldn't even know they use Rust under the hood. The next pages in this series will show my experience in learning how
to do that (without knowing Rust beforehand!) and also the limitations to this approach.

A few remarks to discuss beforehand, to get some of the caveats out of the way..

### Why is Rust faster than Python?
The main relevant difference between Rust and Python in this regard is that Rust is a compiled and statically-typed 
language and Python is not. This makes it that some of the complexities in what needs to happen are presolved by the 
Rust syntax and compiler, while the Python interpreter has to do that live while running the code. Think of adding two 
integers x and y together. In the case of Rust, the compiler will have made sure that the exact procedure that has to be
followed has been defined, so when you run your code, it can just do that procedure. In the case of Python, a lot of 
questions (might*) have to be answered: are both x and y really numbers, and are they really integers? What do we do in
case the answer is no? Should we already take the possibility that the answer is no into account beforehand? This 
results in more decision points needing to be figured out at every step, and decision points slow every process down
(not just in code).

### Does this only hold for Rust?
No, there are many other choices you could make instead of Rust as well. An obvious one is C, which is the language used
for a lot of core Python functions themselves (Python is not written in Python, or, not only in Python). There are some
advantages and disadvantages of Rust over C. The main disadvantage of Rust is that for C, due to its historic presence, 
there are more resources out there than for Rust: more example code, more IDE integration, and also more fellow 
programmers that can help you out. 

The main advantage (although it depends who you ask) is that Rust is annoying. And annoying is good if you are new to
statically-typed languages. There are also moments where the harsh rules of Rust could slow you down compared to using
something like C, but these are less likely if you are just aiming to replace a single core Python function with a piece
of external code in Rust or C. Instead, you are likely to often forget about the details of this lower-level language,
and then it helps you that someone will remind you of what you are doing wrong. 

In a different use case, your choice of language might be different, but my experience was that I was able to within
half a day was able (from scratch!) to learn some basic Rust, and learn how to call Rust form Python to speed-up a 
pre-existing function to less than 10% of its run time, and that the Rust compiler (and me shutting down their 
complaints) made me more confident that this solution actually works for a variety of input values.

### What is coming up?
This page tries to answer the "Why Rust?" question. What follows will be answers to the "How to Rust" question:
- How to get started with Rust, and Rust in Python
- How to apply this to a pre-existing function
- How to include Rust code in your Python package
- How to incorporate this into your Python toolset going forward

These pages will be more code-based and less text-based, but they will also involve my own experiences and suggestions.
You can use them together with existing documentation (which can be a bit scattered all over the place).

Of course, you can use this series as a starting point of learning more Rust (which is also what I did!). Or as a
starting point for learning other languages and integrate them in Python (which is also what I have planned!). But you
have to start somewhere, and it's nice to get to some tangible gain that you can incorporate into an existing codebase
or workflow. So for that first step, go to the [first page](../rust-python-01) of this series.
