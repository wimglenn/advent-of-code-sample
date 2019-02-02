advent-of-code-sample:
----------------------

This repository provides a working example plugin structure for using the `aoc` runner script provided by [advent-of-code-data](https://github.com/wimglenn/advent-of-code-data). You could fork this repo and edit it, or just write your own plugin manually.

The `aoc` runner allows you to easily verify your [Advent of Code](https://adventofcode.com/) solutions against multiple datasets, or verify other user's code against your own dataset.

```bash
$ cat ~/.config/aocd/tokens.json  # create this file with some auth tokens
{
    "github": "53616c7465645f5f0775...",
    "google": "53616c7465645f5f7238...",
    "reddit": "53616c7465645f5ff7c8...",
    "twitter": "53616c7465645f5fa524..."
}
$ pip install ~/src/advent-of-code-sample  # install the directory which contains your setup.py file
...
$ pip install -q advent-of-code-wim  # can also install some other user's code if you want..?
...
$ aoc --years 2015 --days 3 4 11    # run it!
   0.25s   2015/3  - Perfectly Spherical Houses in a Vacuum            wim/github    ✔ part a: 2565                             ✔ part b: 2639
   0.11s   2015/3  - Perfectly Spherical Houses in a Vacuum            wim/google    ✔ part a: 2592                             ✔ part b: 2360
   0.12s   2015/3  - Perfectly Spherical Houses in a Vacuum            wim/reddit    ✔ part a: 2592                             ✔ part b: 2360
   0.12s   2015/3  - Perfectly Spherical Houses in a Vacuum            wim/twitter   ✔ part a: 2565                             ✔ part b: 2639
   0.12s   2015/3  - Perfectly Spherical Houses in a Vacuum     myusername/github    ✖ part a: 1234 (expected: 2565)            ✖ part b: 5678 (expected: 2639)
   0.12s   2015/3  - Perfectly Spherical Houses in a Vacuum     myusername/google    ✖ part a: 1234 (expected: 2592)            ✖ part b: 5678 (expected: 2360)
   0.11s   2015/3  - Perfectly Spherical Houses in a Vacuum     myusername/reddit    ✖ part a: 1234 (expected: 2592)            ✖ part b: 5678 (expected: 2360)
   0.11s   2015/3  - Perfectly Spherical Houses in a Vacuum     myusername/twitter   ✖ part a: 1234 (expected: 2565)            ✖ part b: 5678 (expected: 2639)
   9.04s   2015/4  - The Ideal Stocking Stuffer                        wim/github    ✔ part a: 254575                           ✔ part b: 1038736
  25.43s   2015/4  - The Ideal Stocking Stuffer                        wim/google    ✔ part a: 117946                           ✔ part b: 3938038
  12.20s   2015/4  - The Ideal Stocking Stuffer                        wim/reddit    ✔ part a: 254575                           ✔ part b: 1038736
  47.67s   2015/4  - The Ideal Stocking Stuffer                        wim/twitter   ✔ part a: 282749                           ✔ part b: 9962624
   0.12s   2015/4  - The Ideal Stocking Stuffer                 myusername/github    ✖ part a: 1234 (expected: 254575)          ✖ part b: 5678 (expected: 1038736)
   0.12s   2015/4  - The Ideal Stocking Stuffer                 myusername/google    ✖ part a: 1234 (expected: 117946)          ✖ part b: 5678 (expected: 3938038)
   0.12s   2015/4  - The Ideal Stocking Stuffer                 myusername/reddit    ✖ part a: 1234 (expected: 254575)          ✖ part b: 5678 (expected: 1038736)
   0.12s   2015/4  - The Ideal Stocking Stuffer                 myusername/twitter   ✖ part a: 1234 (expected: 282749)          ✖ part b: 5678 (expected: 9962624)
   6.17s   2015/11 - Corporate Policy                                  wim/github    ✔ part a: vzbxxyzz                         ✔ part b: vzcaabcc
   6.26s   2015/11 - Corporate Policy                                  wim/google    ✔ part a: cqjxxyzz                         ✔ part b: cqkaabcc
   4.69s   2015/11 - Corporate Policy                                  wim/reddit    ✔ part a: hxbxxyzz                         ✔ part b: hxcaabcc
   5.75s   2015/11 - Corporate Policy                                  wim/twitter   ✔ part a: hxbxxyzz                         ✔ part b: hxcaabcc
   0.11s   2015/11 - Corporate Policy                           myusername/github    ✖ part a: 1234 (expected: vzbxxyzz)        ✖ part b: 5678 (expected: vzcaabcc)
   0.12s   2015/11 - Corporate Policy                           myusername/google    ✖ part a: 1234 (expected: cqjxxyzz)        ✖ part b: 5678 (expected: cqkaabcc)
   0.11s   2015/11 - Corporate Policy                           myusername/reddit    ✖ part a: 1234 (expected: hxbxxyzz)        ✖ part b: 5678 (expected: hxcaabcc)
   0.12s   2015/11 - Corporate Policy                           myusername/twitter   ✖ part a: 1234 (expected: hxbxxyzz)        ✖ part b: 5678 (expected: hxcaabcc)
```

How to hook into your code:
---------------------------

The `aoc` runner uses setuptools' [dynamic discovery of services and plugins](https://setuptools.readthedocs.io/en/latest/setuptools.html#dynamic-discovery-of-services-and-plugins) features, *entry points*, to locate and run your code. See [https://entrypoints.readthedocs.io/](https://entrypoints.readthedocs.io/) for more info about creating/finding plugins using entry points in Python.

In your package, define your plugin's entry point in your `setup.py`, `setup.cfg`, or `pyproject.toml`. The group name to use is "adventofcode.user", for example:

```python
# setup.py
from setuptools import setup

setup(
    ...
    entry_points={"adventofcode.user": ["myusername = mypackage:mysolve"]},
)
```

Change `mypackage` to whatever package or module name is used to import your stuff.
The name `mysolve` should resolve to a callable in your package's namespace which accepts three named arguments `year`, `day`, `data` (any order ok) and returns two values, e.g.:

```python
def mysolve(year, day, data):
    ...
    return part_a_answer, part_b_answer
```

Inside the entry-point you can do whatever you need in order to delegate to your code. For example, write out data to a scratch file then run a script, or import a function and just pass in the data directly as an argument. The only requirement is that this entry-point should return a tuple of two values, with the answers for that day's puzzle - the rest is up to you.
