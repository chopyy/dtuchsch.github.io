---
layout: post
title:  "clang-format for automatic code-formatting and a consistent code-style"
date:   2016-08-02 23:00:00
categories: C++, code-style, clang-format
---

> `clang-format` is a formatting tool, which automatically formats your cpp files and header files based on a specified code-style. It is part of the clang compiler toolchain.

# Requirements

To install the tool `clang-format` type `$ sudo apt-get install clang-format` in a terminal.

# clang-format from the command line

Tool `clang-format` is usable from the command line. To adjust one source file based on the LLVM code-style type, the following command is used:

```
$ clang-format -style=LLVM -i <file>
```
Option -i will do an in-place edit. This means, the changes will be made immediatly within the source file.

For more info about `clang-format` options have a look at [ClangFormat Documentation](http://clang.llvm.org/docs/ClangFormat.html).

# Create your own .clang-format file

It's more interesting to use your own code-style. For your own code-style you need a `.clang-format` file. To create a `.clang-format` file, option `-dump-config` is used:

```
$ clang-format -style=llvm -dump-config > .clang-format
```

This command will create a `.clang-format` file based on the LLVM code-style. You now may adjust the file to your needs. Be sure to name your file `.clang-format`. Tool `clang-format` only looks for a `.clang-format` file in the directory of your source files or in one of its parent directories.

To adjust one source file based on your `.clang-format` file you need the following command:

```
$ clang-format -style=file -i <file>
```

The option `-style=file` will search for a `.clang-format` file in one of its parent directories.

# Integration in Robot Operating System (ROS)

Place the `.clang-format` file into the parent directory of a ROS package.

# Integration in Atom editor

There is an extension package for the [GitHub Atom editor](https://atom.io/) available.

* Go to **Edit > Preferences**. This will open a new tab *Settings*.
* Switch to **Install**, type in `clang-format` in the *Search Packages* field and install it.
* You will find the installed package under **Packages**.

My personal settings are:

* Format C on Save
* Format C Plus Plus on Save
* Style: Default: file

This will execute `clang-format` every time you save a C/C++ header or source file in Atom, based on a `.clang-format` file. If there is no file available, the standard LLVM code-style will be applied.

# References

* [My personal .clang-format file](https://gist.github.com/dtuchsch/8f16ef8c05fd0113f75d60a3dea53482)
* [ClangFormat Documentation](http://clang.llvm.org/docs/ClangFormat.html)
* [Build your own clang-format file interactively](https://clangformat.com/)
* Chandler Carruth from Google gave a great overview about clang-format at his [GoingNative 2013 talk](https://channel9.msdn.com/Events/GoingNative/2013/The-Care-and-Feeding-of-C-s-Dragons).
