## Winter

Winter is the default package manager for the Utopia operating system, its job is to install and update **non-critical system components**, this means, Winter can't update itself directly, as Winter is consider a critical system component.

### Introduction

Winter is designed with the idea of Symlink Style Package Management, you can read more about this approach in the [LFS guide about package management](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/pkgmgt.html), in a nutshell, Winter is designed with the idea the user might want to install multiple versions of a package.

The basic idea behind Winter, is to be Utopia specific and benefit from the changes we have mentioned in previous chapters, like NeoFHS.

Winter takes the approach we mentioned in the introduction, all programs are self-contained in their own directories, to understand what this means, lets take a moment to deep dive in a traditional UNIX system (This could be FreeBSD, Linux, etc) that happens to have a package manager (like dnf, apt, pkg, zypper) now lets suppose you wish to install `libfoo`, doing this is rather easy, you just write something like `sudo package_manager install libfoo`, this would install libfoo into a directory, lets suppose this one was `/usr/lib/libfoo.so`, while at first glance this looks okay, and allows the developer to start working, lets suppose we have a common problem that we as developers might face: The software we are working on, hasn't been updated to the latest version of the software, what this means is, our program `bar` hasn't been updated to use `libfoo 2.0` but rather uses `libfoo 1.0`, this means here, we are left with two options:

- Try to update the software to use `libfoo 2.0` and then pull request/submit it to a review (in case it is a company)
- Use `libfoo 1.0`

Now, the problem with the first approach, is that we are developers lose more time trying to update the simple utlity `bar` to support `libfoo 2.0` and if it's the case of company software or not open source software, we are going to have a harder time trying to get some teams to accept our changes, so the best idea is, at least for now: use `libfoo 1.0`, now here we face our first issue, most package managers don't keep older versions of libraries (except for say, OpenSSL), and even if they do, most of their build systems aren't designed for this, for example, lets suppose `bar` is open source software, their build system, has to make it explict they are going to compile against an specific version of the library, so it would look something like this: 

```
build do:
    ./configure --with-libfoo=/usr/lib/libfoo1.so --with-libfoo-include=/usr/include/libfoo1
```

Now, here we can get into some serious _dependency hell_, because while `bar` might only depend on `libfoo 1.0` it doesn't mean `libfoo 1.0` doens't have older software dependencies, this means that now we have to modify the build file of `libfoo 1.0` to point to the older versions that said software depends on, and we can't be sure one of those dependencies isn't using older dependencies too.

Even trying to downgrade to avoid the case mentioned above, will end up in broken dependencies and in term a broken OS, because while our software depends on `libfoo 1.0` it doesn't mean everyone does.

While the example above mentions a clear example with an older library being required, some of the times, it might be an older version of a certain _toolchain_ or _programming language_ that is required, while certain tools like `pyenv` have emerged to counter this issue, we would like to see something at the system level.

And we aren't clueless about what we are doing either, we aren't the first ones trying to implement something like this (thankfully) here are some examples of other projects that work this way, and benefit from it:

- Homebrew (The most popular package manager for macOS)
- NIX
- GUIX
- GoboLinux

Some others have picked up the idea and created a sandbox-like environment:

- Flatpak
- Snap

While some others have approached this problem creating diverse containers, an example of this for developers would be [Docker](https://docker.io)

Winter solves this problem, and it also creates the following benefits: 

- Easy to uninstall programs: Thanks to the level of integration that Winter has with the Operating System (A benefit we can have because we control all of the userland), you can just remove or "trash" a Program under "/Programs" and Winter will remove the rest.
- Virtually no conflicts: Because every package is self-contained, it is possible to install two otherwise conflicting packages (Like the example we mentioned above)
- Intuitive design: Stop wondering what package installed that new file in your `/lib` directory, with Winter, you know exactly what files belong to what program.

### A little view inside Winter

Please note: This isn't a full view into the Winter internals, to read more about the Winter internals, you can go to Winter's own book by clicking [here](https://utopiaos.github.io/winter/book)

Winter installs its packages to `/Programs`, there are stored using the following structure: `/Programs/ProgramName/Version` so for example. python 3.8 would be stored like this: `/Programs/python/3.8` inside said folder, we would be able to find the typical directories a Python installation might have:

```
.
├── bin/
│   ├── 2to3
│   ├── pydoc
│   └── python
├── include/
│   └── python3.8
├── lib64/
│   └── python3.8
└── share/
    └── doc
```

The depicted above is of course not the full directory tree, as the default directory tree is thousands of files (2566 to be exact), now here is where symlinks come handy again, Winter then links the files to the `/System/Index`, so for example: 

```
/Programs/python/3.8/bin/python         -> /System/Index/bin/python
/Programs/python/3.8/bin/2to3           -> /System/Index/bin/2to3
/Programs/python/3.8/bin/pydoc          -> /System/Index/bin/pydoc
/Programs/python/3.8/include/python3.8  -> /System/Index/include/python3.8
/Programs/python/3.8/lib64/python3.8    -> /System/Index/lib/python3.8
/Programs/python/3.8/share/doc          -> /System/Index/share/doc
```

As you can see, the way Winter installs and stores packages, allows you to select which version is _linked_ to the System Index, for example, one could install Python 3.9, on doing so, you might see the folders `3.8` and `3.9` subdirectories, then, one could perform the corresponding Winter command to select the version that you wish to link.

If you would like to read about each Winter command and what it does you can click [here](https://utopiaos.github.io/winter/book).

Some other benefits weren't described here in order to avoid extreme complexity, for example [Winter environmnets](https://utopiaos.github.io/winter/book) weren't described here, but this is a feature of Winter to setup certain versions of packages depending on the directory the process might be running on.