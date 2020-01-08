# CleverCSV: A Clever CSV Package

[![Travis Build Status](https://travis-ci.org/alan-turing-institute/CleverCSV.svg?branch=master)](https://travis-ci.org/alan-turing-institute/CleverCSV)
[![PyPI version](https://badge.fury.io/py/clevercsv.svg)](https://pypi.org/project/clevercsv/)
[![Documentation Status](https://readthedocs.org/projects/clevercsv/badge/?version=latest)](https://clevercsv.readthedocs.io/en/latest/?badge=latest)
[![Downloads](https://pepy.tech/badge/clevercsv)](https://pepy.tech/project/clevercsv)
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/alan-turing-institute/CleverCSVDemo/master?filepath=CSV_dialect_detection_with_CleverCSV.ipynb)

*CleverCSV provides a drop-in replacement for the Python* ``csv`` *package 
with improved dialect detection for messy CSV files. It also provides a handy 
command line tool that can standardize a messy file or generate Python code to 
import it.*

**Useful links:**

- [CleverCSV on Github](https://github.com/alan-turing-institute/CleverCSV)
- [CleverCSV on PyPI](https://pypi.org/project/clevercsv/)
- [Demo of CleverCSV on Binder (interactive!)](https://mybinder.org/v2/gh/alan-turing-institute/CleverCSVDemo/master?filepath=CSV_dialect_detection_with_CleverCSV.ipynb)
- [Paper (PDF)](https://gertjanvandenburg.com/papers/VandenBurg_Nazabal_Sutton_-_Wrangling_Messy_CSV_Files_by_Detecting_Row_and_Type_Patterns_2019.pdf)
- [Paper (HTML)](https://rdcu.be/bLVur)
- [Reproducible Research Repo](https://github.com/alan-turing-institute/CSV_Wrangling/)
- [Blog post on messy CSV files](https://towardsdatascience.com/handling-messy-csv-files-2ef829aa441d)<sup>NEW!</sup>

## Introduction

- CSV files are awesome! They are lightweight, easy to share, human-readable, 
  version-controllable, and supported by many systems and tools!
- CSV files are terrible! They can have many different formats, multiple 
  tables, headers or no headers, escape characters, and there's no support for 
  recording metadata!

CleverCSV is a Python package that aims to solve some of the pain points of 
CSV files, while maintaining many of the good things. The package 
automatically detects (with high accuracy) the format (*dialect*) of CSV 
files, thus making it easier to simply point to a CSV file and load it, 
without the need for human inspection. In the future, we hope to solve some of 
the other issues of CSV files too.

CleverCSV is [based on 
science](https://gertjanvandenburg.com/papers/VandenBurg_Nazabal_Sutton_-_Wrangling_Messy_CSV_Files_by_Detecting_Row_and_Type_Patterns_2019.pdf). 
We investigated thousands of real-world CSV files to find a robust way to 
automatically detect the dialect of a file. This may seem like an easy 
problem, but to a computer a CSV file is simply a long string, and every 
dialect will give you *some* table. In CleverCSV we use a technique based on 
the patterns of the parsed file and the data type of the parsed cells. With 
our method we achieve a 97% accuracy for dialect detection, with a 21% 
improvement on non-standard (*messy*) CSV files.

We think this kind of work can be very valuable for working data scientists 
and programmers and we hope that you find CleverCSV useful (if there's a 
problem, please open an issue!) Since the academic world counts citations, 
please **cite CleverCSV if you use the package**. Here's a BibTeX entry you 
can use:

```bib
@article{van2019wrangling,
        title = {Wrangling Messy {CSV} Files by Detecting Row and Type Patterns},
        author = {{van den Burg}, G. J. J. and Naz{\'a}bal, A. and Sutton, C.},
        journal = {Data Mining and Knowledge Discovery},
        year = {2019},
        volume = {33},
        number = {6},
        pages = {1799--1820},
        issn = {1573-756X},
        doi = {10.1007/s10618-019-00646-y},
}
```

And of course, if you like the package please *spread the word!* You can do 
this by Tweeting about it 
([#CleverCSV](https://twitter.com/hashtag/clevercsv)) or clicking the ⭐️ [on 
GitHub](https://github.com/alan-turing-institute/CleverCSV)!

## Installation

The package is available on PyPI:

```bash
$ pip install clevercsv
```

## Usage

CleverCSV consists of a Python library and a command line tool called 
``clevercsv``.

### Library

We designed CleverCSV to provide a drop-in replacement for the built-in CSV 
module, with some useful functionality added to it. Therefore, if you simply 
want to replace the builtin CSV module with CleverCSV, you can import 
CleverCSV as follows, and use it as you would use the builtin [csv 
module](https://docs.python.org/3/library/csv.html).

```python
import clevercsv
```

CleverCSV provides an improved version of the dialect sniffer in the CSV 
module, but it also adds some useful wrapper functions. These functions 
automatically detect the dialect and aim to make working with CSV files 
easier. We currently have the following helper functions:

* [detect_dialect](https://clevercsv.readthedocs.io/en/latest/source/clevercsv.html#clevercsv.wrappers.detect_dialect): 
  takes a path to a CSV file and returns the detected dialect
* [read_csv](https://clevercsv.readthedocs.io/en/latest/source/clevercsv.html#clevercsv.wrappers.read_csv): 
  automatically detects the dialect and encoding of the file, and returns the 
  data as a list of rows.
* [csv2df](https://clevercsv.readthedocs.io/en/latest/source/clevercsv.html#clevercsv.wrappers.csv2df): 
  detects the dialect and encoding of the file and then uses Pandas to read 
  the CSV into a DataFrame.

Of course, you can also use the traditional way of loading a CSV file, as in 
the Python CSV module:

```python
# importing this way makes it easy to port existing code to CleverCSV!
import clevercsv as csv

with open("data.csv", "r", newline="") as fp:
  # you can use verbose=True to see what CleverCSV does:
  dialect = csv.Sniffer().sniff(fid.read(), verbose=False)
  fp.seek(0)
  reader = csv.reader(fp, dialect)
  rows = list(reader)
```

That's the basics! If you want more details, you can look at the code of the 
package, the test suite, or the [API 
documentation](https://clevercsv.readthedocs.io/en/latest/source/modules.html).

### Command-Line Tool

The ``clevercsv`` command line application has a number of handy features to 
make working with CSV files easier. For instance, it can be used to view a CSV 
file on the command line while automatically detecting the dialect. It can 
also generate Python code for importing data from a file with the correct 
dialect. The full help text is as follows:

```text
USAGE
  clevercsv [-h] [-v] [-V] <command> [<arg1>] ... [<argN>]

ARGUMENTS
  <command>       The command to execute
  <arg>           The arguments of the command

GLOBAL OPTIONS
  -h (--help)     Display this help message.
  -v (--verbose)  Enable verbose mode.
  -V (--version)  Display the application version.

AVAILABLE COMMANDS
  code            Generate Python code for importing the CSV file.
  detect          Detect the dialect of a CSV file
  help            Display the manual of a command
  standardize     Convert a CSV file to one that conforms to RFC-4180.
  view            View the CSV file on the command line using TabView
```

Each of the commands has further options (for instance, the ``code`` command 
can generate code for importing a Pandas DataFrame). Use
``clevercsv help <command>`` for more information. Below are some examples for 
each command:

#### Code

Code generation is useful when you don't want to detect the dialect of the 
same file over and over again. You simply run the following command and copy 
the generated code to a Python script!

```text
$ clevercsv code imdb.csv

# Code generated with CleverCSV

import clevercsv

with open("imdb.csv", "r", newline="", encoding="utf-8") as fp:
    reader = clevercsv.reader(fp, delimiter=",", quotechar="", escapechar="\\")
    rows = list(reader)
```

We also have a version that reads a Pandas dataframe:

```text
$ clevercsv code --pandas imdb.csv

# Code generated with CleverCSV

import clevercsv

df = clevercsv.csv2df("imdb.csv", delimiter=",", quotechar="", escapechar="\\")
```

#### Detect

Detection is useful when you only want to know the dialect.

```text
$ clevercsv detect imdb.csv
Detected: SimpleDialect(',', '', '\\')
```

The ``--plain`` flag gives the components of the dialect on separate lines, 
which makes combining it with ``grep`` easier.

```text
$ clevercsv detect --plain imdb.csv
delimiter = ,
quotechar =
escapechar = \
```

#### Standardize

Use the ``standardize`` command when you want to rewrite a file using the 
RFC-4180 standard:

```text
$ clevercsv standardize --output imdb_standard.csv imdb.csv
```

In this particular example the use of the escape character is replaced by 
using quotes.

#### View

This command allows you to view the file in the terminal. The dialect is of 
course detected using CleverCSV! Both this command and the ``standardize`` 
command support the ``--transpose`` flag, if you want to transpose the file 
before viewing or saving:

```text
$ clevercsv view --transpose imdb.csv
```


## Contributors

Code:

* [Gertjan van den Burg](https://gertjan.dev)

Scientific work:

* [Gertjan van den Burg](https://gertjan.dev)
* [Alfredo Nazabal](https://scholar.google.com/citations?user=IanHvT4AAAAJ)
* [Charles Sutton](https://homepages.inf.ed.ac.uk/csutton/)


## Contributing

If you want to encourage development of CleverCSV, the best thing to do now is 
to *spread the word!*

If you encounter an issue in CleverCSV, please open an issue or submit a pull 
request. Don't hesitate, you're helping to make this project better! If 
GitHub's not your thing but you still want to contact us, you can send an 
email to gertjanvandenburg at gmail dot com instead.

## Notes

License: MIT (see LICENSE file).

Copyright (c) 2019 [The Alan Turing Institute](https://turing.ac.uk).

