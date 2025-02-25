# pypandoc

[![Build Status](https://github.com/JessicaTegner/pypandoc/actions/workflows/ci.yaml/badge.svg)](https://github.com/JessicaTegner/pypandoc/actions/workflows/ci.yaml)
[![GitHub Releases](https://img.shields.io/github/tag/JessicaTegner/pypandoc.svg)](https://github.com/JessicaTegner/pypandoc/releases)
[![Pypandoc PyPI Version](https://img.shields.io/pypi/v/pypandoc?label=pypandoc+pypi+version)](https://pypi.org/project/pypandoc/)
[![Pypandoc Binary PyPI Version](https://img.shields.io/pypi/v/pypandoc_binary?label=pypandoc+binary+pypi+version)](https://pypi.org/project/pypandoc_binary/)
![PyPandoc PyPi Downloads](https://img.shields.io/pypi/dm/pypandoc)
![PyPandoc Binary PyPI Downloads](https://img.shields.io/pypi/dm/pypandoc_binary)
[![conda version](https://anaconda.org/conda-forge/pypandoc/badges/version.svg)](https://anaconda.org/conda-forge/pypandoc/)
[![Development Status](https://img.shields.io/pypi/status/pypandoc.svg)](https://pypi.python.org/pypi/pypandoc/)
[![PyPandoc Python version](https://img.shields.io/pypi/pyversions/pypandoc.svg)](https://pypi.python.org/pypi/pypandoc/)
[![PyPandoc Binary Python version](https://img.shields.io/pypi/pyversions/pypandoc_binary.svg)](https://pypi.python.org/pypi/pypandoc_binary/)
![License](https://img.shields.io/pypi/l/pypandoc.svg)

Pypandoc provides a thin wrapper for [pandoc](https://pandoc.org), a universal
document converter.

## Installation

Pypandoc uses pandoc, so it needs an available installation of pandoc. Pypandoc provides 2 packages, "pypandoc" and "pypandoc_binary", with the second one including pandoc out of the box.
The 2 packages are identical, with the only difference being that one includes pandoc, while the other don't.

If pandoc is already installed (i.e. pandoc is in the `PATH`), pypandoc uses the version with the
higher version number, and if both are the same, the already installed version. See [Specifying the location of pandoc binaries](#specifying-the-location-of-pandoc-binaries) for more.

To use pandoc filters, you must have the relevant filters installed on your machine.

### Installing via pip

If you [want to install pandoc yourself](#Installing-pandoc) or are on a unsupported platform, you'll need to install "pypandoc" and  [install pandoc manually](#Installing-pandoc)

```
pip install pypandoc
```


If you want pandoc included out of the box, you can utilize our pypandoc_binary package, which are identical to the "pypandoc" package, but with pandoc included.

```
pip install pypandoc_binary
```

Prebuilt [wheels for Windows and Mac OS X](https://pypi.python.org/pypi/pypandoc_binary/)

If you use Linux and have [your own wheelhouse](https://wheel.readthedocs.org/en/latest/#usage),
you can build a wheel which include pandoc with
`python setup_binary.py download_pandoc; python setup.py bdist_wheel`. Be aware that this works only
on 64bit intel systems, as we only download it from the
[official releases](https://github.com/jgm/pandoc/releases).

### Installing via conda

Pypandoc is included in [conda-forge](https://conda-forge.github.io/). The conda packages will
also install the pandoc package, so pandoc is available in the installation.

Install via `conda install -c conda-forge pypandoc`.

You can also add the channel to your conda config via
`conda config --add channels conda-forge`. This makes it possible to
use `conda install pypandoc` directly and also lets you update via `conda update pypandoc`.

### Installing pandoc

If you don't already have pandoc on your system, or have installed the pypandoc_binary package, which includes pandoc, you need to install pandoc by yourself.

#### Installing pandoc via pypandoc

Installing via pypandoc is possible on Windows, Mac OS X or Linux (Intel-based, 64-bit):

```python
pip install pypandoc
from pypandoc.pandoc_download import download_pandoc
# see the documentation how to customize the installation path
# but be aware that you then need to include it in the `PATH`
download_pandoc()
```

The default install location is included in the search path for pandoc, so you
don't need to add it to the `PATH`.

By default, the latest pandoc version is installed. If you want to specify your own version, say 1.19.1, use `download_pandoc(version='1.19.1')` instead.

#### Installing pandoc manually

Installing manually via the system mechanism is also possible. Such installation mechanism
make pandoc available on many more platforms:

- Ubuntu/Debian: `sudo apt-get install pandoc`
- Fedora/Red Hat: `sudo yum install pandoc`
- Arch: `sudo pacman -S pandoc`
- Mac OS X with Homebrew: `brew install pandoc pandoc-citeproc Caskroom/cask/mactex`
- Machine with Haskell: `cabal-install pandoc`
- Windows: There is an installer available
  [here](https://pandoc.org/installing.html)
- [FreeBSD with pkg:](https://www.freshports.org/textproc/hs-pandoc/) `pkg install hs-pandoc`
- Or see [Pandoc - Installing pandoc](https://pandoc.org/installing.html)

Be aware that not all install mechanisms put pandoc in the `PATH`, so you either
have to change the `PATH` yourself or set the full `PATH` to pandoc in
`PYPANDOC_PANDOC`. See the next section for more information.

### Specifying the location of pandoc binaries

You can point to a specific pandoc version by setting the environment variable
`PYPANDOC_PANDOC` to the full `PATH` to the pandoc binary
(`PYPANDOC_PANDOC=/home/x/whatever/pandoc` or `PYPANDOC_PANDOC=c:\pandoc\pandoc.exe`).
If this environment variable is set, this is the only place where pandoc is searched for.

In certain cases, e.g. pandoc is installed but a web server with its own user
cannot find the binaries, it is useful to specify the location at runtime:

```python
import os
os.environ.setdefault('PYPANDOC_PANDOC', '/home/x/whatever/pandoc')
```

## Usage

There are two basic ways to use pypandoc: with input files or with input
strings.


```python
import pypandoc

# With an input file: it will infer the input format from the filename
output = pypandoc.convert_file('somefile.md', 'rst')

# ...but you can overwrite the format via the `format` argument:
output = pypandoc.convert_file('somefile.txt', 'rst', format='md')

# alternatively you could just pass some string. In this case you need to
# define the input format:
output = pypandoc.convert_text('# some title', 'rst', format='md')
# output == 'some title\r\n==========\r\n\r\n'
```

`convert_text` expects this string to be unicode or utf-8 encoded bytes. `convert_*` will always
return a unicode string.

It's also possible to directly let pandoc write the output to a file. This is the only way to
convert to some output formats (e.g. odt, docx, epub, epub3, pdf). In that case `convert_*()` will
return an empty string.

```python
import pypandoc

output = pypandoc.convert_file('somefile.md', 'docx', outputfile="somefile.docx")
assert output == ""
```


It's also possible to specify multiple input files to pandoc, either as absolute paths, relative paths or file patterns.

```python
import pypandoc

# convert all markdown files in a chapters/ subdirectory.
pypandoc.convert_file('chapters/*.md', 'docx', outputfile="somefile.docx")

# convert all markdown files in the book1 and book2 directories.
pypandoc.convert_file(['book1/*.md', 'book2/*.md'], 'docx', outputfile="somefile.docx")

# convert the front from another drive, and all markdown files in the chapter directory.
pypandoc.convert_file(['D:/book_front.md', 'book2/*.md'], 'docx', outputfile="somefile.docx")
```


pathlib is also supported.

```python
import pypandoc
from pathlib import Path

# single file
input = Path('somefile.md')
output = input.with_suffix('.docx')
pypandoc.convert_file(input, 'docx', outputfile=output)

# convert all markdown files in a chapters/ subdirectory.
pypandoc.convert_file(Path('chapters').glob('*.md'), 'docx', outputfile="somefile.docx")

# convert all markdown files in the book1 and book2 directories.
pypandoc.convert_file([*Path('book1').glob('*.md'), *Path('book2').glob('*.md')], 'docx', outputfile="somefile.docx")
# pathlib globs must be unpacked if they are inside lists.
```

In addition to `format`, it is possible to pass `extra_args`.
That makes it possible to access various pandoc options easily.

```python
output = pypandoc.convert_text(
    '<h1>Primary Heading</h1>',
    'md', format='html',
    extra_args=['--atx-headers'])
# output == '# Primary Heading\r\n'
output = pypandoc.convert_text(
    '# Primary Heading',
    'html', format='md',
    extra_args=['--base-header-level=2'])
# output == '<h2 id="primary-heading">Primary Heading</h2>\r\n'
```

pypandoc now supports easy addition of
[pandoc filters](https://pandoc.org/scripting.html).

```python
filters = ['pandoc-citeproc']
pdoc_args = ['--mathjax',
             '--smart']
output = pypandoc.convert_file(filename,
                               to='html5',
                               format='md',
                               extra_args=pdoc_args,
                               filters=filters)
```

Please pass any filters in as a list and not as a string.

Please refer to `pandoc -h` and the
[official documentation](https://pandoc.org/MANUAL.html) for further details.

## Dealing with Formatting Arguments

Pandoc supports custom formatting though `-V` parameter. In order to use it through
pypandoc, use code such as this:

```python
output = pypandoc.convert_file('demo.md', 'pdf', outputfile='demo.pdf',
  extra_args=['-V', 'geometry:margin=1.5cm'])
```

> Note: it's important to separate `-V` and its argument within a list like that or else
it won't work. This gotcha has to do with the way
[`subprocess.Popen`](https://docs.python.org/2/library/subprocess.html#subprocess.Popen) works.

## Logging Messages

Pypandoc logs messages using the [Python logging library](https://docs.python.org/3/library/logging.html).
By default, it will send messages to the console, including any messages
generated by Pandoc. If desired, this behaviour can be changed by adding
[handlers](https://docs.python.org/3/library/logging.html#handler-objects) to
the pypandoc logger **before calling any functions**. For example, to mute all
logging add a [null handler](https://docs.python.org/3/library/logging.handlers.html#nullhandler):

```python
import logging
logging.getLogger('pypandoc').addHandler(logging.NullHandler())
```

## Getting Pandoc Version

As it can be useful sometimes to check what pandoc version is available at your system or which
particular pandoc binary is used by pypandoc. For that, pypandoc provides the following
utility functions. Example:

```
print(pypandoc.get_pandoc_version())
print(pypandoc.get_pandoc_path())
print(pypandoc.get_pandoc_formats())
```

## Related

* [pydocverter](https://github.com/msabramo/pydocverter) is a client for a service called
[Docverter](https://www.docverter.com), which offers pandoc as a service (plus some extra goodies).
* See [pyandoc](https://pypi.python.org/pypi/pyandoc/) for an alternative implementation of a pandoc
wrapper from Kenneth Reitz. This one hasn't been active in a while though.
* See [panflute](https://github.com/sergiocorreia/panflute) which provides `convert_text` similar to pypandoc's. Its focus is on writing and running pandoc filters though.

## Contributing

Contributions are welcome. When opening a PR, please keep the following guidelines in mind:

1. Before implementing, please open an issue for discussion.
2. Make sure you have tests for the new logic.
3. Make sure your code passes `flake8 pypandoc/*.py tests.py`
4. Add yourself to contributors at `README.md` unless you are already there. In that case tweak your contributions.

Note that for citeproc tests to pass you'll need to have [pandoc-citeproc](https://github.com/jgm/pandoc-citeproc) installed. If you installed a prebuilt wheel or conda package, it is already included.

## Contributors

* [Jessica Tegner](https://github.com/JessicaTegner) - New maintainer as of 1. Juli 2021
* [Valentin Haenel](https://github.com/esc) - String conversion fix
* [Daniel Sanchez](https://github.com/ErunamoJAZZ) - Automatic parsing of input/output formats
* [Thomas G.](https://github.com/coldfix) - Python 3 support
* [Ben Jao Ming](https://github.com/benjaoming) - Fail gracefully if pandoc is missing
* [Ross Crawford-d'Heureuse](https://github.com/rosscdh) - Encode input in UTF-8 and add Django
  example
* [Michael Chow](https://github.com/machow) - Decode output in UTF-8
* [Janusz Skonieczny](https://github.com/wooyek) - Support Windows newlines and allow encoding to
  be specified.
* [gabeos](https://github.com/gabeos) - Fix help parsing
* [Marc Abramowitz](https://github.com/msabramo) - Make `setup.py` fail hard if pandoc is
  missing, Travis, Dockerfile, PyPI badge, Tox, PEP-8, improved documentation
* [Daniel L.](https://github.com/mcktrtl) - Add `extra_args` example to README
* [Amy Guy](https://github.com/rhiaro) - Exception handling for unicode errors
* [Florian Eßer](https://github.com/flesser) - Allow Markdown extensions in output format
* [Philipp Wendler](https://github.com/PhilippWendler) - Allow Markdown extensions in input format
* [Jan Katins](https://github.com/jankatins) - Handling output to a file, Travis to work on newer version of pandoc, return code checking, get_pandoc_version. Helped to fix the Travis build, new `convert_*` API. Former maintainer of pypandoc
* [Aaron Gonzales](https://github.com/xysmas) - Added better filter handling
* [David Lukes](https://github.com/dlukes) - Enabled input from non-plain-text files and made sure tests clean up template files correctly if they fail
* [valholl](https://github.com/valholl) - Set up licensing information correctly and include examples to distribution version
* [Cyrille Rossant](https://github.com/rossant) - Fixed bug by trimming out stars in the list of pandoc formats. Helped to fix the Travis build.
* [Paul Osborne](https://github.com/posborne) - Don't require pandoc to install pypandoc.
* [Felix Yan](https://github.com/felixonmars) - Added installation instructions for Arch Linux.
* [Kolen Cheung](https://github.com/ickc) - Implement `_get_pandoc_urls` for installing arbitrary version as well as the latest version of pandoc. Minor: README, Travis, setup.py.
* [Rebecca Heineman](https://github.com/burgerbecky) - Added scanning code for finding pandoc in Windows
* [Andrew Barraford](https://github.com/abarrafo) - Download destination.
* [Jesse Widner](https://github.com/jwidner) & [Dominic Thorn](https://github.com/domvwt) - Add support for lua filters
* [Alex Kneisel](https://github.com/hey-thanks/) - Added pathlib.Path support to convert_file.
* [Juho Vepsäläinen](https://github.com/bebraw/) - Creator and former maintainer of pypandoc
* [Connor](https://github.com/DisSupEng/) - Updated Dockerfile to Python 3.9 image and added docker compose file
* [Colin Bull](https://github.com/colinbull) - Added ability to control whether files are sorted before being passed to pandoc process.
* [Kurt McKee](https://github.com/kurtmckee) - Project infrastructure improvements

## License

Pypandoc is available under MIT license. See LICENSE for more details. Pandoc itself is [available under the GPL2 license](https://github.com/jgm/pandoc/blob/master/COPYING.md).
