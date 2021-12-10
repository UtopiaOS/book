# Introduction

Welcome to _The Utopia Operating System_, an introductory book about Utopia. The Utopia Operating System, helps you experience a better, simpler and more cohesive computer experience. Operating systems are either too advanced or too simple. Utopia challenges that conflict. Through the community, discussions and personal experiences, Utopia manages to find a balance, between the power user and the user, Utopia gives you the power to control your operating system and computer, without the hassle associated with such control.

## Who is Utopia for

Utopia is ideal for many people for a varaiety of people, however, in the next sections we look at the biggest and most important ones.

## Casual Users

Utopia is made for the casual user that might their computer to:
- Watch cat videos
- Chat with some friends
- Use a web browser
- Edit some document file
- Play a casual game

When using Utopia the computer becomes a tool, Utopia is made for users with varying levels of knowledge about computers, and casual users aren't an execption to the Utopia operating system, with Utopia, the user is free to forget about what Operating system he is using. All thanks to the many tools and principles Utopia follows, the casual user can benefit from principles such as:

- **Apps are self contained bundles**: In Utopia, applications are just folders that contain the "executable", is not expected to the user to know what any of that means, he just needs to know, he has to right click an app to open it and start enjoying the diverse variety of options the app might bring to his systems.
- **The global menu**: In Utopia, an user does not need to get user to the hambuguer or the custom menu each app might have, in Utopia, the top bar contains a global menu, which handles all the options the user might have for interacting with the application.
- **The store**: The store, is powered by libigloo, a UI-first package manager library, this makes the store experience, fast, simple and seamless, because it was designed to be specifically used with the backend and frontend, the user doesn't need to worry about the store showing him weird terminal outputs, or options he doesn't understand, going to the store, is just like in the real world.
- **The dock**: In Utopia, the favorite apps of the user, are always stored in the bottom-bar also known as the dock, the dock is a centric place, that tells you what applications are open, the option to close them, pin them.
- **The lauchpad**: The launchpad is a familiar phone-like homescreen that allows the user to see all the apps he hass installed on his computer, and remove them, sort them, or even quickly search to his liking.

By using these tools and interfaces, the average user can feel at home while using his computer.

## Power users

Utopia is made for the power users that might use their computer to:
- Do heavy gaming
- Tweak the computer to their likings
- Modify the desktop or other aspects of the operating system
- Block certain websites from loading
- Modify system services 
- Modify low level system settings

When using Utopia as a power user, your computer is completely yours, you have the power and freedom to disasble, all the systems that prevent you from modifying your system, but as always Utopia empowers you, the power user by providing tools and defaults that might your experience better.

- **Windows apps run by default**: Utopia uses sytemd-winefmt to run windows apps with a double click, while we can't ensure they are going to work as expected, basic apps should give the user an awesome experience out of the box.
- **System wide configuration api**: The user can format about dotfiles, or having thousands of files in different locations with different formats, Utopia provides a way for you, the developer, to integrate your settings directly into the system in an intuitive way, and for those who like files, Utopia translates settings from it's Database to a FUSE mountpoint in /Settings full of TOML files, so it's like the user has the classic config files.
- **Scripting**: Utopia empowers the users to perform certain actions, while at the time of writting this document is only planned for moving files around, the Utopia big brother file watcher, allows the user to run POSIX-compliant scripts when a certain action is performed.
- **A package manager that empowers the user**: Winter is a highly costumizable package manager, you're free to add third party repos, change the vendor of some packages, install propiteraty software, change your kernel, make your own packages, etc, all while being sure, the SAT solver ensures your dependencies dont break.
- **Multiple versions of the same app**: The power user can forget about doing crazy workarounds to run older versions of the app, just so he can have features that might be removed in newer versions, Utopia is designed so users can run multiple versions of the app by default.
- **A terminal that helps you**: Utopia ships with a third party terminal, named warp, it combines the aspects of a classic text-based interface and the modernity of having buttons, quick actions and tasks.

