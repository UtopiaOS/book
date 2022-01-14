## NeoFHS

NeoFHS stands for Neo (Greek for new) File Hierarchy Standard, it builds up on the old [FreeDesktop File Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard), and tries to make it more intuitive, providing human-readable names in the English language by default.

### Introduction

A File Hierarchy Standard (FHS) refers to the way files are stored on your computer. As a Linux user you might be familiar with `/home` being the home/user folder, `/dev` being for special or device files, and `/bin` being where executable files are stored. Someone from Windows would expect `C:\Program Files` (system) or `%localappdata%` (user) to contain programs and the user folders to be in `C:\Users`. The Utopia Developers believe that the FHS has to be clear and concise so that the end user doesn't have any trouble finding things and doesn't get confused by system files that don't matter to them.


The NeoFHS tries to simplify things and remove legacy directories or decisions that where made just to accommodate fragmentation like the /usr merge. It is heavily inspired and based on the [GoboLinux](https://gobolinux.org) FHS, but with some improvements in the organization and separation of some types of software. For example in Utopia /Programs (CLI) and /Applications (GUI) are separate.

NeoFHS is built with these ideas in mind:
- A mother path (root path) should always be capitalized (`/Programs`, `/System`)
- A path should always be human-readable (this means no `/usr` or `/etc`) and use common and simple English words
- Paths should be explicit in their name about what they contain

Now that we have discussed the reasoning and principals behind the NeoFHS, we can start examining how a common Utopia System is organized.

```
.
├── Data/               -> Resources that belong to the System, Individual programs or even the user
│   ├── Temporary       -> Data that is ereased on next boot, or at any given moment, non critical information
│   └── Variable/       -> Spool files, log files, etc.
│       ├── cache
│       ├── empty
│       ├── lib
│       ├── lock
│       ├── log
│       ├── run
│       └── spool
├── Core/               -> Indispensable and immutable core of the operating system
│   ├── Boot            -> EFI, bootloader, etc.
│   ├── Libraries       -> Core System libraries, like the libc
│   └── Binaries        -> Core System binaries, like cat, ls, sh, etc. 
├── System/             
│   ├── Settings        -> System or Program config files that are propagated globally.
│   ├── Kernel/         
│   │   ├── Status      -> Kernel status files (part of the /proc fileystem)
│   │   ├── Modules     -> Kernel modules that load after the kernel boots
│   │   └── Objects     -> A view of the kernel's device tree (/sys)
│   ├── Devices         -> Device files managed by udev
│   ├── Library         -> Fonts, Frameworks, Sounds, and other third party additions
│   └── Index/          -> Links to the legacy tree 
│       ├── bin
│       ├── include
│       ├── lib
│       ├── libexec
│       └── share/
│           ├── consolefonts
│           ├── fonts
│           └── man/
│               ├── info
│               └── man{1-9}
├── Applications        -> Sandboxed programs that have a GUI
├── Programs            -> CLI/unsandboxed programs that run on the CLI
├── Users               -> The users home folder
└── Volumes             -> Mountpoints for filesystems or volumes (USBs, External drives)
```

As you can see above the new FHS attempts to be more intuitive, using plain English and benefiting from the fact our modern computers can handle very large paths.

Having a modernized FHS is beneficial for some decisions in utopia. For example, this makes having multiple versions of the same package possible. It also has benefits for some Utopia decisions, for example, thanks to this design it is possible to apply a Symlink Style Package Manager, which allows users to have multiple versions of the same package.

### Surviving alongside the legacy tree

One of the first things that might concern you when examining this new FHS is compatibility for literally all programs. The simple solution to this is to use [Symbolic Links](https://en.wikipedia.org/wiki/Symbolic_link). We use symlinks (Symbolic Links) to "link" the new directories to the legacy ones and hide the legacy directories from the user. These links are basically shortcuts that programs follow when they try to use the legacy FHS, so when a program tries to read `/etc` it instead reads `/System/Settings`. By using these symlinks we can keep compatibility with the legacy file hierarchy while still presenting a modern hierarchy to the user. **Programs that have not updated to support NeoFHS will continue to work on Utopia without the user noticing.** This is a great compromise since we cannot expect all programs to add dedicated support to Utopia.

Below are legacy FHS directories followed by what they are symlinked to. Click [here](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard). For an explanation of what these directories are for.

```
/etc            -> /System/Settings
/boot           -> /Core/Boot
/var            -> /Data/Variable
/tmp            -> /Data/Temporary
/home           -> /Users
/proc           -> /System/Kernel/Status
/dev            -> /System/Devices
/sys            -> /System/Kernel/Objects
/bin            -> /System/Index/bin
/lib            -> /System/Index/lib
/lib64          -> /System/Index/lib
/sbin           -> /System/Index/bin
/usr/bin        -> /System/Index/bin
/usr/sbin       -> /System/Index/bin
/usr/share      -> /System/Index/share
/usr/libexec    -> /System/Index/libexec
/usr/include    -> /System/Index/include
```

As a developer making programs for Utopia we prefer you support NeoFHS but if you don't include dedicated support these symbolic links will make sure your program works fine. Everything should work as you expect, so if your program reads a file in `/etc/settings.toml` filesystem magic will redirect your calls to `/System/Settings/settings.toml`.

As a User, you do not want to see all these legacy directories, so we need to hide them. Linux itself does not have a hidden file system built in like Windows and macOS (putting `.` in front of these directories is not ideal), but GoboLinux provides a solution for this that we can use that works seamlessly and cleanly.

While you can read about the solution GoboLinux used [here](https://gobolinux.org/doc/articles/gobohide.html), in a nutshell, it keeps a list of the `inodes` in a kernel linked list. After this, when performing the `readdir()` syscall, the kernel verifies if said inode is on the `inode` list. If this is true, it won't be copied to the destination buffer.
