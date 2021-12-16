### NeoFHS

NeoFHS stands for Neo (Greek for new) File Hierarchy Standard, it builds up on the old [FreeDesktop File Hiearchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard), and tries to make it more intuive, providing human readable names in the english language by default.

A file hiearchy standard, refers to the way diverse system components are stored across the computer, for example, if you come from a typical Linux background, you might be familiar with executables being on `/bin`or `/usr/bin` your user being on `/home`, or if you come from Windows, you might be used to programs being on `C:\Program Files`, in the same way, Utopia defines it's own FHS, where it expects diverse files to be located, and where the end user, might expect to find things.

The NeoFHS tries to simplify things and remove legacy directories or desicisions that where made just to acomodate the fragmentation (such as the /usr merge), it is heavily inspired by [GoboLinux](https://gobolinux.org) but with some improvements over the top, mainly, in the organization and separation of some types of software (For example, in Utopia /Programs and /Applications are different things).

NeoFHS is built with this ideas in mind:
- A mother path (root path) should always be capitalized (`/Programs`, `/System`)
- A path should always be human readable (no `/usr` or `/etc`) using common and simple english words
- Paths should be explicit in their name about what they contain

