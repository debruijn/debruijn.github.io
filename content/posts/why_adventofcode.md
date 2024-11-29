+++
title = "Advent of Code is coming. Why I participate again and again."
date = 2024-11-29
draft = false

[taxonomies]
categories = ["blog"]
tags = ["advent of code", "programming"]

[extra]
lang = "en"
toc = true
comment = true
copy = true
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
featured = false
reaction = false
+++

[Advent of Code](http://www.adventofcode.com) is coming up again, starting December 1st. It is a yearly event with programming
challenges every day up until Christmas, and I have participated in it for last few years and will do so again this
year. In case you are on the fence, let me give you some reasons on why it can be worthwhile to participate, for all
kinds of level of programming.

Note: if you read this after it has started, that is no problem! You can do the challenges retroactively, not just for
the current edition but also for the past 9 years.

TLDR:
- Learn more about programming, no matter at what level you are right now
- Learn new programming languages
- Try out new approaches
- Learn new algorithms and solutions
- Learn your limits
- It is fun!

### How does it work?
Every day at midnight EST a new challenge will be available to work on. You can also always do older days if you missed 
one, or do them out of order. Each challenge consists of two parts: if you complete the first one, you will get access
to the second part. Sometimes the second part is a totally different problem to solve than the first part, but in most
cases it will be a more difficult version: bigger data set, more iterations, more rules, higher dimension, or whatever
is applicable to the challenge of that day.

Also note that it is not just about programming, but also about generic problem-solving: what kind of assumptions can
you make? What kind of properties should the solution have, and how can you get there? Is there any hint in your data
input on what corner cases to take into account? In a sense, programming is always at least a bit about problem solving
(how do I get the desired output from this input?) but this particular property might not be what you expect if you
just think about programming challenges.

You can participate by joining up with an existing GitHub, Google, Twitter or Reddit account. Each challenge will have
an input that is specific to you, which you can manually download from the website (or automatically using a tool like
Python has in [advent-of-code-data](https://github.com/wimglenn/advent-of-code-data) - whatever language you want to
use is bound to have one as well!).

There are leaderboards for the fastest to complete the challenge, but don't worry about that if you are new to it: first
take your time and do the challenges **well** before you try to do them **quick**. So, having the background out of the
way: why should you join?

### Get started with programming - or get back to it
First of all, the event can be a great way to learn more about programming, or getting back to it. If you are 100% new to
programming, then first pick a language (if you don't know which one, then probably Python or JavaScript are good 
starters) and learn the most basic skills in that language first. If you can't figure out how to start with such a 
language on your own, then that is **totally fine**, but something like Advent of Code might not be the thing for you -
at least not now. Find someone to help you get started, and then you can always come back to this later. But if you are 
already past that stage (e.g. you know how to sum a few numbers or how to print a text on screen) then this can be a 
good push towards expanding your skills.

Note that the later days can become more tricky, so don't feel bad if you will encounter days that you honestly just 
can't solve. Feel free to skip a day, to come back to this later, or to just stop at the point it is no longer worth it
for you. On the other hand: if you have the time and energy to push through a few more days, then you might have pushed
your limits further out, so it's worth considering not giving up.

Similarly, if it has been a while since you regularly wrote some code, you can use this to refresh some of those skills.
I have done something similar recently with a similar coding event called [coding quest](http://www.codingquest.io), for which
I have done the first series of practice challenges all in R, a language I used to work with daily, but which I used
only rarely in recent years. I could notice a big difference between the first and last challenge in remembering the 
how to do something in R, so for sure if an R project is coming up for me, I will do something similar to freshen it up.

By the way: programming can be defined very broadly in this sense. Most problems can be solved using tools that would
not normally be called programming languages, like Excel or even visualisations of the data in Minecraft! So if
something like Python looks scary to you, but you know your way around Excel: have a go with that! 

### Level up your go-to programming language
On the other hand, if you already are programming every day, then the Advent of Code event can be a good motivation to 
try out parts of the language you don't tend to use a lot on a day-to-day basis but would be good to know more about. 

For me, this has been the first thing I really enjoyed in Advent of Code. When I first got introduced to it, I was in a
situation in which I used Python daily, but was mostly focused on Python packages for data processing (back then, 
numpy and pandas) and Bayesian modeling (pystan, numpyro), but I rarely used actual standard Python. The scripts at my
work were also mostly procedural back then, so I didn't really use classes that often. In those first years of me doing
Advent of Code, I focused on the following topics (in roughly the following order):
- Object-oriented programming and classes (on days for which this was a suitable approach)
- Pure python without dependencies - so stay away from numpy and pandas which I used to tend towards
- Making use of functions in standard modules like:
  - [itertools](https://docs.python.org/3/library/itertools.html) (for example: cycle, chain, pairwise, combinations)
  - [functools](https://docs.python.org/3/library/functools.html) (for example: cache, reduce, cmp_to_key, partial)
  - [collections](https://docs.python.org/3/library/collections.html) (for example: Counter, deque, defaultdict)

I currently can't imagine not having those solutions in my tool kit, even as my preferred first solutions 
(when suitable), and it is because I have build up that habit over time doing all those Advent of Code challenges. 
Of course, for specific use cases using packages makes a lot of sense, but having the urge to import numpy or pandas 
just to sum a list of numbers was not the right habit.

### Learn new languages
Advent of Code can also be great to start with new languages that you don't use in your daily life. This can be a 
language that you consider a good skill to obtain, but it can also purely be out of interest. Learning a new language
will always also come with learning the way of thinking of that language, which can also benefit you in your go-to 
language for specific problems.

For me, this "other" language was always Rust. I wanted to jump on the hype train but didn't really have a good reason
to get into it in work. In the last year, I have done so, and I have [blogged about](/posts/rust-python-00) before.
Advent of Code was critical in this as a motivation: there were a few days for which my Python solve was a bit too slow
for my liking, and I was curious if I could speed it up by running key calculations in Rust. With the most basic Rust
skills imaginable, I figured out how to run that with `maturin` in Python and got a major speed boost. But then.. I got
hooked on programming in Rust. Of course, you can gain even more speed by putting the entire process in Rust, instead of
just the key calculations. So now I have several puzzles of AoC 2020 solved in Python, in Rust, and in a combination of
both.

The nice thing about learning a new language using Advent of Code is that the challenges will present to you new
situations that require you to investigate the best way to do them in this new language; but not all at once. One day
you might be looking into how to convert characters to numbers, then after you might have to implement your own hashing
algorithm, and the day after you have to somehow navigate a grid. Different challenges at different levels, that will
all help you build up your skill set.

### Learn new programming concepts or algorithms
This brings us to another benefit: all kinds of fields that make use of programming are introduced and involved into the
challenges, which means that you will build up a nice set of solutions and skills for a wide variety of topics.
Depending on your background, you might already be familiar with some of the following, but likely not all:
- How to build your own hardware emulator
- Working with CPU instructions
- Pathfinding problems and all kinds of algorithms (Dijkstra, A*, etc)
- Identifying states of convergence in a set of equations/instructions
- Various variants of Game of Life
- (and many more)

Related to this, is that you can use this to more generally think about data structures: when to use a deque instead of 
a list, for example. Writing your code and calculations in a way to be more efficient will surely find a way to be
worthwhile in your daily life at some point. Some people come back to the same puzzle again and again to squeeze out
another bit of performance increase. It is your choice how far you want to take that of course (if at all).

### Learn your limits (or lack thereof)
Like said before: it is totally okay if at some point you check out. Some people only do the first few days, others hang
on until halfway through, and only a small subset complete the full 25 days after 25 days. It doesn't hurt to see which
days are easy for you, which days are hard-but-doable, and which ones are just out of your league right now. You can
use this knowledge to identify what to work on to push your limits, but you don't have to. For example, if you learn
through Advent of Code that you really dislike pathfinding problems, then you also know to avoid those in future job
opportunities. It is good to also know the limits of your interest. For me, I have the feeling I would not enjoy
working with CPU instructions (`jump 4`, `goto 19`, etc) based on my enjoyment of those set of problems in Advent of
Code, so I know what to avoid. :)

### It is fun to solve problems!
Even if you have no value in learning more about programming, or if you think there is nothing to learn, there is also
that bit of enjoyment of solving a puzzle of any kind. This is why people like things like jigsaw puzzles, sudoku's, or
puzzle video games. That same feeling of accomplishment can be had with Advent of Code and similar events. This might be
the most useful tool in evaluating how long to stick to the event: participate while you are still having fun, and when
you don't, perhaps persevere a couple more days but don't force yourself to stick it out if it is too much of a burden 
after 5 days already.

### Some tips if you are new to it
- In most cases it is ideal to figure out a solution that would work for any input, so don't (permanently) hard code in
some specific numbers or properties of your input. But sometimes, looking at patterns in the data is part of the 
challenge, so if you can't figure out a general solution, then it might be such a day where you actually have to 
construct a data-specific solution.
- Speaking of input data: if you want to make use of Git repositories for managing your code, then don't include the
input files within them if you make the repositories public. Eric (the creator of Advent of Code) has requested to keep 
the files private, because that is his creative property. So either keep the whole repository private, or figure out
some way to not include the files (gitignore, git private submodules, caching data in a different folder, etc).
- When you have a first solution of both parts, have a look back yourself at what you could have done better.
  - Is there some restructuring that will perform better?
  - Is there some calculation that is not needed?
  - Is there some calculation that you reuse between parts 1 and 2, and that you can refactor to deduplicate your code?
  - Are there other cleanups to be done?
  - Do you think another solution could exist that performs significantly better in the same programming language?
- .. and then: have a look at the advent of code [subreddit](http://www.reddit.com/r/adventofcode) for how other people have
solved the problem.
  - Of course, don't go here until you have your answer, unless you are really stuck. (But even if you are stuck, first
take a break and see if you can figure out later - you learn more from finding your own answer!)
  - Especially have a look at people using the same language as you: have they done basically the same approach? If so,
is there anything to learn from their coding style? If they used a different approach: do you think that would be
faster? Can you see how to include that in your code as well? Sometimes there are small things to pick up from others.
    - For me, an example in Rust is to make use of type declarations like `type Pt = Point<isize,2>` so I don't have to
type out the full type every time. This makes coding faster but also more readable. I have picked this up from someone
else by looking at their solution.
    - Alternatively, you might have struggled to think of your own algorithm while there is actually a class of generic
algorithms that this falls under. Recognizing that that's the case can help you understand future problems and see the
similarities and differences quicker.
- There will be problems with grids, especially two-dimensional grids for path-finding problems (and others). As an 
example, think of a maze that you have to find the quickest route through. To work with coordinates in two dimensions,
in many languages it can be a tip to work with complex numbers instead. So for example, in Python a `(x, y)` tuple would
become a single `x + y*1j` complex number. Rotation can then be done by multiplying with `1j` or `-1j`, instead of 
manually shifting the coordinates around.
  - Alternatively, you can also do some prep work and write your own Grid object/struct/class/functions/whatever. This
would allow you to not have to fuss around with processing the input but instead get to solving the actual problem 
quickly. Such a generic Grid object might then also allow you to work with more than 2 dimensions (which is sure to
come up at least once as well!).
- Another tip for grids: think about whether for this problem it makes more sense to keep track of all elements, or only
of relevant elements.
  - All elements: e.g. if you have a list of lists of elements, you can do: `if grid[i][j] == '.'`
  - Only relevant elements: e.g. a set of (i, j) coordinates, `if (i, j) in grid`, or `if i + j*1j in grid` if you use complex numbers
  - Especially when the ruleset is such that the grid size can grow, keeping track of only relevant elements can be faster and easier
  - But if instead you have a fixed grid size for which you know each element is relevant, then maintaining a set can be
  more cumbersome and slower
  - Alternatively to both, if there are a fixed number of elements for which you have to track the location, you can 
  also just have a list or dictionary of current locations.
- Final tip: don't use AI to get to solutions. At least be responsible with it and use it in such a way that you think
you can learn something from it. The goal is not to just find the right number, but also to gain the skills such that
you can find it with less effort next time. Using AI will not help you learn to get there, and the AI doesn't need you
to get there as well. It is much more useful to struggle with a challenge yourself, and if needed, come back to it 
later. Of course, in the end it is your call what you want to get out of it.
  - What might be a good idea though is to show AI your solution when you are done, and see if they have any additional
suggestions. This can especially be useful if you use a language for which no other solutions are posted on Reddit.

### More events
If you are getting the taste of these coding events and you want to do them throughout the year (or you are looking for
additional challenges after you have finished the Advent of Code one of that day), consider the following:
- Of course, old Advent of Code challenges are waiting for you to pick them up. Each year has a slightly different focus
of a type of problem that is more recurring, but overall the type of challenges across all years are quite comparable.
To find them, click `Events` at the top of the Advent of Code page or directly go [here](https://adventofcode.com/events)
- I can also highly recommend [Everybody Codes](http://everybody.codes) which had its first event this November 2024 with
puzzles that are very similar in style compared to Advent of Code (each day consists of 3 parts instead of 2 - but often
two parts are very similar).
- There is also [Coding Quest](http://www.codingquest.io), for which I have only done the introductory set of puzzles so far
but I plan to come back to this in the new year with new languages to learn.
- This December 2024 will also feature the kick-off of [Advent of SQL](http://www.adventofsql.com), which will also introduce daily puzzles
but these will be setup to be solved with (Postgres) SQL. I can't vouch for its quality yet since it still has to launch
as of writing this page, but I hope this will be a good way to dust off those SQL skills. :)
