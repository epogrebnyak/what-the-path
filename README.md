# justpath

[![Reddit](https://img.shields.io/badge/Reddit-%23FF4500.svg?style=flat&logo=Reddit&logoColor=white)][reddit]
[![PyPI - Version](https://img.shields.io/pypi/v/justpath)](https://pypi.org/project/justpath/)
[![CI](https://github.com/epogrebnyak/justpath/actions/workflows/python-package.yml/badge.svg)](https://github.com/epogrebnyak/justpath/actions/workflows/python-package.yml)

[reddit]: https://www.reddit.com/r/Python/comments/1aehs4i/clean_path_of_nonexistent_directories_with/

Explore and correct PATH environment variable on both Windows and Linux.

## Basic usage

What directories are part of PATH?

```console
justpath show
```

Sort them alphabetically:

```console
justpath show --sort
```

What are the paths with `bin`?

```console
justpath show --includes bin
```

Are there any errors on PATH?

```console
justpath show --errors
```

What is the correct PATH with no errors?

```console
justpath show --correct --string
```

## Useful cases

### 1. Filter directory names

`justpath` allows to filter paths that must or must not include a certain string.
Filtering is case insensitive, `--includes windows` and `--includes Windows` will
produce the same result.

```console
λ justpath show --sort --includes windows --excludes system32
39 C:\Users\Евгений\AppData\Local\Microsoft\WindowsApps
24 C:\WINDOWS
14 C:\Windows
46 C:\tools\Cmder\vendor\git-for-windows\cmd
47 C:\tools\Cmder\vendor\git-for-windows\mingw64\bin
12 C:\tools\Cmder\vendor\git-for-windows\usr\bin
```

### 2. Directory does not exist or not a directory

`justpath` will indicate if path does not exist or path is not a directory.

Below is an example from Github Codespaces, for some reason
`/usr/local/sdkman/candidates/ant/current/bin` does not exist,
but included in `PATH`.

```console
λ justpath show --sort --includes sdkman
19 /usr/local/sdkman/bin
23 /usr/local/sdkman/candidates/ant/current/bin (directory does not exist)
21 /usr/local/sdkman/candidates/gradle/current/bin
20 /usr/local/sdkman/candidates/java/current/bin
22 /usr/local/sdkman/candidates/maven/current/bin
```

Added file `touch d:\quarto\this_is_a_file` for example below.

```console
λ justpath show --includes quarto
33 C:\Program Files\Quarto\bin
41 D:\Quarto\bin
50 x:\quarto\does_not_exist (directory does not exist)
51 d:\quarto\this_is_a_file (not a directory)
```

Use `--errors` flag to explore what is parts of PATH are not valid and why.

```console
λ justpath show --includes quarto --errors
50 x:\quarto\does_not_exist (directory does not exist)
51 d:\quarto\this_is_a_file (not a directory)
```

### 3. Purge invalid paths

`--correct` flag will drop invalid paths from listing.

```console
λ justpath show --includes quarto --correct
33 C:\Program Files\Quarto\bin
41 D:\Quarto\bin
```

### 4. Dump `PATH` as JSON

`justpath` you can dump a list of paths from `PATH` to JSON.
You may add `--correct` flag to list only correct paths.

```
justpath show --correct --json
```

### 5. Create new content string for `PATH`

You can get a valid string for your PATH in a format native to your operating system
using `--string` ouput flag.

```console
λ justpath show --correct --string
C:\tools\Cmder\bin;C:\tools\Cmder\vendor\bin;C:\Windows\system32;C:\Windows;...
```

## Installation

### Stable version

```console
pip install justpath
```

### Development version

```console
git clone https://github.com/epogrebnyak/what-the-path.git
cd what-the-path
pip install -e .
```

or shorter:

```console
pip install git+https://github.com/epogrebnyak/what-the-path.git
```

## CLI tools

After installation you will get two aliases for the same command line tool.

Preferred:

```
justpath --help
```

Being depreciated:

```
pathit --help
```

## Motivation

I think [this quote][quote] about `PATH` is quite right:

> I always get the feeling that nobody knows what a PATH is and at this point they are too afraid to ask.

[quote]: https://www.reddit.com/r/linuxquestions/comments/pgv7hm/comment/hbf3bno/

PATH environment variable syntax on Windows and on Linux does scare me a bit,
so I wrote this utility to be able to explore PATH more easily.

## Development notes

- Neither `justpath` nor any child process cannot modify your shell `PATH`, just view it.
- See [links.md](docs/links.md) for more tribal knowledge about `PATH`.

## Alternatives

Even better tools than `justpath` may exist.

- On Linux you can run `echo $PATH | tr ";" "\n" | sort | grep bin` instead of trivial parts of `justpath`.
- [Rapid Environment Editor](https://www.rapidee.com/en/path-variable) for Windows is a gem (no affiliation, just a thankful user).
- Maybe some smart command-line utility in Rust will emerge for PATH, but [not there yet](https://gist.github.com/sts10/daadbc2f403bdffad1b6d33aff016c0a).
- There is [pathdebug](https://github.com/d-led/pathdebug) written in Go that goes a step futher and attempts to trace where your PATH is defined.
