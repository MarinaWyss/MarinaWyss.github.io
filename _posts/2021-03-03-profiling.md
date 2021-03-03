---
title: "Profiling Python Code"
date: 2021-03-03
tags: [python]
excerpt: "Fixing slow code."
---

One of my projects at work recently has been transitioning one of our pipelines to use a new Python class. Upon testing it out, I discovered that the new class is *much* slower than the old one. But the reason why wasn’t immediately obvious.

So, I looked into profiling the code. A profile is a set of statistics that describes how often and for how long various parts of the program executed.

I first tried `cProfile`, which returns stats on:

* The total number of function calls
* The total number of primitive (not induced by recursion) function calls
* The number of calls per function
* The time spent per function

`cProfile` can be called in-line on an individual function using simple syntax:
```
import cProfile

cProfile.run('2 + 2')
# or
cProfile.run('func_to_profile()')
```

Or in the command line for an entire script:
```
python -m cProfile [-o output_file] [-s sort_order] (-m module | myscript.py)
```

The results can be printed, or formatted into reports via the `pstats` module. There are lots of other fancy options if you choose to use `cProfile` as a class, or use it with other packages to create nifty tree graph visualizations.

In my case it looked like the slowdown was largely because pandas was running many built-in methods. It was tricky to diagnose exactly where this was happening, though, so I turned to the `line_profiler` package instead, which gives execution time information on line-by-line. 

```
from line_profiler import LineProfiler

lp = LineProfiler()
lp_wrapper = lp(func_to_profile)
lp_wrapper(func_args)
lp.print_stats()
```

This was useful, and I was able to fix some obvious slowdowns using this method.
