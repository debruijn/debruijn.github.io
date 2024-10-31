+++
title = "Rust in Python part 3: publish a Python package"
date = 2024-10-31

[taxonomies]
tags = ["Blog", "Rust", "Python"]
+++

Publishing a Python package with Rust in it
- Intervalues package in Python
- Reimplementing in Rust and speed comparisons
- Publishing a package:
  - Setup tomls and project structure and setuptools-rust
  - Got stuck on version file
  - To publish on PyPI, need to build with older GCC compiler to produce manylinux wheels
- Alternative approaches to try:
  - Directly publishing with maturin publish