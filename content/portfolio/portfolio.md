+++
title = "Side-projects and papers"
date = 2024-11-05

[taxonomies]
tags = ["Portfolio", "Side-projects", "Publications"]
+++

## Side projects

### intervalues and related
`intervalues` is a Python and Rust package to efficiently combine intervals. What sets it apart from other similar
packages is being able to track input _values_ for these _intervals_, hence the name. Currently, there is a standalone
Rust package, and a Rust-accelerated Python python package. In making these, I also created a new simplified Rust
package to provide a very basic float approximation to circumvent some limitation with floats in Rust.

  - Python intervalues: [Github](https://github.com/debruijn/intervalues) and [PyPI](https://pypi.org/project/intervalues/)
  - Rust intervalues: [Github](https://github.com/debruijn/intervalues_rust) and [crates.io](https://crates.io/crates/intervalues/)
  - intervalues_pyrust: [crates.io](https://crates.io/crates/intervalues_pyrust)
  - intfloat: [Github](https://github.com/debruijn/intfloat) and [crates.io](https://crates.io/crates/intfloat/)

### Counterstrike
One of my hobbies is watching competitive Counterstrike. For ranking CS teams, multiple rankings exist, provided by
the maker of CS Valve, but also by tournament organizer ESL and by CS news and stats website HLTV. I have started a
project to combine these, in 3 ways: (1) put them next to each other; (2) create simple aggregate statistics like
average rank; and (3) producing an "optimal" combined ranking based on these. For this, I had to get access to the
individual rankings as provided by the above parties, which has lead to the `cs-rankings` project 
([Github](https://github.com/debruijn/cs_rankings) / [PyPI](https://pypi.org/project/cs-rankings/)). The project that
uses this for the above 3 goals can be found [here](https://github.com/debruijn/cs2) with
[this](https://github.com/debruijn/cs2/blob/main/combined_cs2_rankings/optimal_score.md) as the main output.

### nl-maps
After moving to Bonaire, a special municipality of the Netherlands located in the Caribbean, I noticed that a lot of
maps of the Netherlands don't show Bonaire (or the other two special municipalities, Saba and Sint Eustatius; the three
together form the BES islands). Although in some cases this is defensible or even logical, in other cases it is simply
an incomplete map, with election maps being the clearest example: for some of the elections like the national elections
or the European elections, the votes on the BES islands has the same value per vote as the ones in the (European)
Netherlands, so a map of "Most voted party per municipality" or "Voter turnout per municipality" without showing the
BES islands is simply not complete. The data is often available to the news network (for example, in the interactive
tool that the NOS has used for the past few elections you can select the three BES municipalities as well as postal
voters from other countries). In order to promote the inclusion in future maps, I have created the package `nl-maps`
([Github](https://github.com/debruijn/nl_maps) / [PyPI](https://pypi.org/project/nl-maps/)) that can help with inserting
the BES islands into existing Dutch informative maps. It can also be used to generate from scratch a map for the 12 
provinces and the 3 BES islands, with support for each individual municipality planned for the future.

### Personal projects
Some of my other personal projects I use for learning a new skill, maintaining a learned skill, or experimenting with 
new ideas can be found on my personal Github [here](https://github.com/debruijn). This includes stuff like my Advent of
Code and Advent of SQL repositories, among others.

<h3>Language stats across these personal repositories</h3>

<a href="https://github.com/debruijn">
<img align="center" src="https://github-readme-stats.vercel.app/api/top-langs/?username=debruijn&layout=compact&theme=buefy&hide_border=false" /></a>

## Research papers and publications
As a result of my PhD at the Tinbergen Institute and Erasmus University Rotterdam, I have been able to work on the
following research papers and publications:
- **Managing Sales Forecasters**, with Philip Hans Franses ([DOI link](10.2139/ssrn.2184281)), _Tinbergen Institute 
  Discussion Paper Series_, December 2012.
- **Forecasting Earnings Forecasts**, with Philip Hans Franses ([Repub link](http://hdl.handle.net/1765/41126)), _Tinbergen 
  Institute Discussion Paper Series_, January 2013.
- **Analyzing fixed-event forecast revisions**, with Chia-Lin Chang, Philip Hans Franses, and Micheal McAleer
  ([DOI link](https://doi.org/10.1016/j.ijforecast.2013.04.002)), _International Journal of Forecasting_, October 2013.
- **Stochastic levels and duration dependence in US unemployment**, with Philip Hans Franses 
  ([Repub link](http://hdl.handle.net/1765/78710)), _Erasmus University Rotterdam Econometric Institute Report Series_, 
  September 2015.
- **Heterogeneous Forecast Adjustment**, with Philip Hans Franses ([DOI link](https://doi.org/10.1002/for.2433)), _Journal
  of Forecasting_, July 2016.
- **Benchmarking judgmentally adjusted forecasts**, with Philip Hans Franses 
  ([DOI link](https://doi.org/10.1002/ijfe.1569)), _International Journal of Finance and Economics_, October 2016.
- **A novel approach to measuring consumer confidence**, with Rene Segers and Philip Hans Franses
  ([DOI link](https://doi.org/10.1016/j.ecosta.2016.11.009)), _Econometrics and Statistics_, September 2017.

More details can be found by following the mentioned DOI or Repub links. Most of my research back then was done using 
Matlab, EViews, Python, and/or LaTeX.