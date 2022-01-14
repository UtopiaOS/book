## Winter

Winter is the [package manager](https://en.wikipedia.org/wiki/Package_manager) for the Utopia Operating System. It's responsible for installing, updating, and managing **non-critical system components**. Calling Winter a package manager is somewhat misleading, as libigloo is the one actually doing all the heavy lifting and Winter is simply the CLI frontend.

The core system is managed separately from Winter to provide stability, consistency and [immutability](https://www.merriam-webster.com/dictionary/immutable). This means that Winter itself, libSystem (libc), systemd, and others are not managed by Winter. You are also expected to use flatpak to install graphical applications at least for now but this may change depending on the paths Utopia takes in the future.

### Introduction

Winter is based around the concept of Symlink Style Package Management, meaning winter is designed with support for multiple package versions which has benefits in many areas like preventing partial upgrades and preventing issues if a library has not been updated upstream. You can read more about this approach in the [LFS guide about package management](https://www.linuxfromscratch.org/lfs/view/stable/chapter08/pkgmgt.html)

Winter is designed to heavily integrate into the Utopia Operating System and take advantage of all the changes we have done, including but not limited to NeoFHS, BigBrother, and the (currently unnamed) global configuration database.

All programs installed with Winter are self-contained and stay in their own directories. Instead of having `libfoo` installed to `/usr/lib/libfoo.so` it would be installed to `/Programs/libfoo/1.0/lib/libfoo.so`. You can still find `libfoo.so` at `/usr/lib/libfoo.so` or `/System/Index/lib/libfoo.so`. As you may have guessed from the version number, Winter supports multiple versions, meaning you can have `/Programs/libfoo/2.0/lib/libfoo.so` at the same time as version 1.0. Supporting multiple versions is beneficial to many people and prevents many problems. A common issue maintainers of distros run into are scenarios where a program relies on an older version of a library, and it cannot be updated (maybe it's a proprietary program or maybe the developer needs more time to work on updating it) but other programs have been updated to `libfoo 2.0`. The maintainer now either has to hold back packages requiring `libfoo 2.0` until the other package is updated or drop the package using the old version of the library. Neither of these scenarios are ideal and inevitably cause dependency hell if a package maintainer doesn't sort this out. By supporting multiple versions, Winter avoids all of this.

A real world scenario is 'bar' is a proprietary program that has not been updated to `libfoo 2.0`. If this mess hasn't been fixed by maintainers of these packages and your package manager only supports one version at a time the result is your package manager trying to downgrade to `libfoo 1.0` which might not be in the distro's repos anymore and even if it was the second you try to downgrade you will encounter dependency issues where you can't downgrade `libfoo` because other packages depend on the latest version. This becomes worse recursively as you realize `libfoo 1.0` might also depend on older versions of other packages and so on. 

While the example above mentions a clear example with an older library being required, sometimes it might be an older version of a certain _toolchain_ or _programming language_ that is required. While certain tools like `pyenv` have emerged to counter this issue, we would like to see something at the system level.

We aren't the first ones trying to implement something like this (thankfully) so we aren't completely in the dark trying to implement this. Here are some examples of other projects that work this way, and reap the benefits:

- Homebrew (The most popular package manager for macOS)
- NIX
- GUIX
- GoboLinux

Some others have picked up the idea and created a sandbox-like environment:

- Flatpak
- Snap
- An example of this marketed towards developers would be [Docker](https://docker.io)

Some miscellaneous benefits of Winter are as follows: 

- Easy to uninstall programs: Thanks to the level of integration that Winter has with the Operating System (A benefit we can have because we control all the userland). You can just remove or "trash" a Program under "/Programs" and Winter will remove the rest.
- Virtually no conflicts: Because every package is self-contained, it is possible to install two otherwise conflicting packages (Like the example we mentioned above)
- Intuitive design: Stop wondering what package installed that new file in your `/lib` directory. With Winter, you know exactly what files belong to what program.
<!--- Maybe remove this one? most package managers provide tools to see what packages made files -->
### A little view inside Winter

Please note: This isn't a full view into the Winter internals. To read more about the Winter internals, you can go to Winter's own book by clicking [here](https://utopiaos.github.io/winter/book)

Winter installs its packages to `/Programs`, Packages are stored using the following structure: `/Programs/ProgramName/Version` so for example. Python 3.8 would be stored like this: `/Programs/python/3.8`. Inside said folder, we would be able to find the typical directories a Python installation might have:

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

The way Winter installs and stores packages allows you to select which version is _linked_ to the System Index with ease. You could install Python 3.9, and you'll see the `3.8` and `3.9` subdirectories, then just perform the corresponding Winter command to select the version that you wish to link.

If you would like to read about each Winter command and what they do you can click [here](https://utopiaos.github.io/winter/book).

Some other benefits weren't described here in order to avoid extreme complexity, for example [Winter environments](https://utopiaos.github.io/winter/book) weren't described here.