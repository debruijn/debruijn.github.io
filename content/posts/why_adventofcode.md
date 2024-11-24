+++
title = "Advent of Code is coming. Why I participate again and again."
date = 2024-11-29
draft = true

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

## Setup - to flesh out
Advent of Code is coming up again. Why participate in such coding events?

- Learn programming (or other tools to solve it), or dust of your skills
- Master your programming language -> itertools, functools (state caching)
- Learn new programming languages
- Learn new programming concepts or algorithms:
  - Building own emulator
  - Interpreting GO TO style languages
  - Pathfinding
  - Convergence in simulated states
- Learn to think about data structures -> when is a list fine, when deque, etc
- Learn your limits: it's fine if at day XX it becomes too much; it's great if you can persevere!

Tips:
- Data is (sometimes) a key component of the solution as well
- Look back when you are done at what you could have done better -> sometimes it can be interesting to experiment
- After the solve, have a look at the solutions on the advent of code subreddit. Especially those in the language you
did. There might be some tricks that you can learn about. And otherwise it can prepare you for future days (but you
have to still recognize it yourself)
  - For example, `type P = Point<isize,2>` for me in Rust
  - First time path finding solved without good algorithm but learned about BFS and DFS then in the comments.
- For 2D grids, in many languages working with complex numbers can be a tip
- Alternatively, you can write some supporting code to pull in that can also work for higher dimensions
  - You can reuse that, of course, and it might show other sides of the language you are using

Warning if you intend to publish solutions on Github or elsewhere: don't include your input data!

Note: Advent of SQL will also start, and you can do Everybody Codes or old AoC years!
