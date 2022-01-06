## NeoFHS

NeoFHS stands for Neo (Greek for new) File Hierarchy Standard, it builds up on the old [FreeDesktop File Hiearchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard), and tries to make it more intuitive, providing human readable names in the english language by default.

### Introduction

A file hiearchy standard, refers to the way diverse system components are stored across the computer, for example, if you come from a typical Linux background, you might be familiar with executables being on `/bin`or `/usr/bin` your user being on `/home`, or if you come from Windows, you might be used to programs being on `C:\Program Files`, in the same way, Utopia defines it's own FHS, where it expects diverse files to be located, and where the end user, might expect to find things.

The NeoFHS tries to simplify things and remove legacy directories or desicisions that where made just to acomodate the fragmentation (such as the /usr merge), it is heavily inspired by [GoboLinux](https://gobolinux.org) but with some improvements over the top, mainly, in the organization and separation of some types of software (For example, in Utopia /Programs and /Applications are different things).

NeoFHS is built with this ideas in mind:
- A mother path (root path) should always be capitalized (`/Programs`, `/System`)
- A path should always be human readable (this means no `/usr` or `/etc`) and use common and simple english words
- Paths should be explicit in their name about what they contain

Now that we have discussed the reasons and principals behind the NeoFHS, we can start examining how a common Utopia System is organized.

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
├── Core/               -> Indispensable, unmutable core parts of the operating system
│   ├── Boot            -> All the EFI files, bootloader, etc.
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
├── Users               -> The users root folder
└── Volumes             -> Mountpoints for filesystems or volumes (USBs, External drives)
```

As you can see above, the new FHS attempts to be more intuitive, using plain english and benefiting from the fact our modern computers can handle very large paths.

It also benefits some Utopia desicions, for example, thanks to this design, it is possible to apply a Symlink Style Package Manager, which allows users to have multiple versions of the same package.

### Surviving alongside the legacy tree

The first problem that might emerge when looking at the new FHS, is, **How the hell does this not break the whole OS?**, and to answer your question, it does break the whole OS, or it would, if it wasn't for the amazing power of [Symbolic Links](https://en.wikipedia.org/wiki/Symbolic_link), thanks to Symlinks (Symbolic Links) we can keep the old file hiearchy there, while also having the benefits of the new one as Applications adapt overtime.

To make the story short, in case you don't know what a symlink is, think about it like a shortcut, yes, the same one your gradma has lots of on her desktop, these shortcuts aren't _really_ the program, but rather, a quickway to access the location of said program, this design, allows you to make `/etc` on the old FHS a "shortcut" to `/System/Settings` on our new FHS.

Below, you can see what parts of the old FHS are symlinked to our new FHS, in case you didn't click on the first link where we mentioned the current FHS, it is recommend you do that now by clicking [here](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard).

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

As you can see above, this implementation allows us to ensure programs don't really need to update directory to support our new FHS (but we would like to), rather, a program can read a file located at `/etc/my_settings.toml` and a little bit of filesystem magic will redirect this call to the real file at `/System/Settings/my_settings.toml`, however, even after this, we still have one big problem: **Repeated folders**, what do we mean with this, is nobody wants to see `/etc` if `/System/Settings`is already thing, however, there is a solution to this, used in diverse operating systems, such as Windows and macOS: **hidden files**, our solution is borrowed from our friends at GoboLinux and it ensures a clean and elegant way of hidding directories.

While you can read about the solution GoboLinux used [here](https://gobolinux.org/doc/articles/gobohide.html) in a nutshell, it keeps a list of the `inodes` in a kernel linked list, after this, when performing the `readdir()` syscall, the kernel verifies if said inode is on the `inode` list, if this is true, it wont be copied to the destination buffer.

