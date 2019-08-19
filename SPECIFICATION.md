AppImage UX proposal
===
###### tags: `AppImage` `UX`

# Abstract

After reviewing the main features of applications management on macOS, it's proposed **the AppImage way** which attempts to bring the best of those features into the GNU/Linux world. It defines that a system must require nothing but FUSE to run an AppImage and desktop integration must be supported of the shelf by file managers, application launchers and other desktop components. To achieve this two main workflows are considered: a software-center-based (or **assisted application management**), and file-manager-based (or **manual application management**). Both approaches must be independent but compatible and capable of coexists.

This UX will have as foundation a common services layer (AppImageService) providing an unique, FreeDesktop standards compatible and coherent implementation of the desktop integration and AppImage management features. Software centers, file managers, launchers and other desktop environment components will consume such services in order to complete the UX.



# Introduction
The AppImage project seeks to introduce the GNU/Linux ecosystem to the best aspects of a macOS-like app installation experience, while avoiding its drawbacks. AppImage seeks to provide an experience even better than of macOS.

But the AppImage project faces the fact that there is not only one GNU/Linux Desktop but rather many, with totally different goals and opinions on how applications should be handled. **Trying to satisfy all of their requirements and restrictions seems to be a nearly impossible task**. It would be easier to make them match the AppImage experience. But to achieve this we must **provide a definition of the AppImage UX and a reference implementation** to be tested, analyzed, improved and adopted


## Characteristics of macOS application structure, distribution, and installation

- Applications consist of a single folder (known as Bundle) that includes all dependencies that are not provided by the base system.

- The system exposes all the special functionalities required to handle Bundles

- Manually downloaded apps are distributed inside disk images or compressed archives to work around the fact that apps are folders, not files
 
- Applications are stored in a centralized location (`/Applications`) that is exposed to the user

- Transparent system integration; manual "installation" consists of dragging the app to the Applications folder and running it

- While system integration is suggested it is not needed. In most cases you can double-click and run an application directly from within the disk image without copying it anywhere first

- The AppStore is the _recommended_ source of Applications

- Applications can be acquired manually from their authors if desired

Additionally we would like to highlight that having an AppStore as source of applications is allowed but not mandatory as it may become a limitation to the distribution or may be used to gather users data. A more complete description of the macOS UX and its implementation along with useful links can be found at: https://github.com/AppImage/appimaged/issues/30

# The AppImage Way: Application usage and distribution on GNU/Linux simplified

Using the macOS application characteristics for reference and inspiration, we will proceed to describe **the AppImage Way**.

## Theory

### Applications as single file
Each AppImage is a single file with all dependencies bundled internally. Installation is a simple download or copy operation, and uninstallation consists simple of deleting the application's file.

Having a single place for storing applications seems to be a good idea and could be achieved by extending `xdg user directories`. But forcing users to use it by changing location of their application files can lead to lots of issues and misunderstandings. So we will subtly encourage users to move AppImages to `$HOME/Applications` but not require it.

From the developer perspective, distributing software capable of running on all GNU/Linux operating systems becomes simple: just produce an AppImage (including signatures and update information) and make it available for download.

### Applications that just work (i.e. assisted applications management)
Users employ applications to help them accomplish their tasks. Most of them don't care where apps are stored or where they come from. They need a simple way of finding new application, and they need verification that an application they are considering installing is 'safe to use'. They need new apps to be ready to be used, launched from the system launcher. And they want their apps to updated based on the developers' schedules, possibly automatically (if desired by the user).

All the above use cases can be satisfied by using a software center client. The main features would be: applications search, download, verification, installation, update notifications and an easy update UX, uninstallation, and metadata inspection.

Users should also be able to update and remove apps using their system-provided application launcher (e.g. KDE Plasma's Kickoff), with the functionality provided via a context menu.

From the developer perspective, AppImages need to be made available to software center clients so they can be shown to users. This would generally entail uploading them to an app repository of some sort, not unlike today's distribution repositories, flathub.org. or opendesktop.org. This content should be moderated and curated by the owners of those repositories to ensure that malicious or broken applications are never exposed to users. But maganing any kind of software store falls out of the AppImage project scope.


### Manual application management for experts
While app management using a graphical software center app is the recommended approach for most users, because AppImages are single files, it is possible for expert users to manage them using the file manager. Features like applications launching, verification, registration, update, metadata extraction and removal should be supported in the file manager, just like in the software center clients and system launcher.

### Benefits
**Typical users** besides all the benefits that the AppImage format provides, users will get a consistent UX in the different desktop environments. Apps will blend nicely into the system showing thumbnail icons, application information, launcher entries, update information and file manager menu entries. In resume their apps will "just work".

**Developers** benefit from full control over the libraries their applications rely on due to the bundling feature; rather than relying on whatever arbitrary library versions are provided by users' operating systems, they can provide the exact versions they have tested their application against, raising quality. Developers also benefit from having one single deployment target that works with all GNU/Linux perating systems, rather than needing to make different packages for different operating systems. Finally, with only a single target, developers can retake control of their packaging, branding, and marketing rather than being at the mercy of dozens of 3rd-party distribution packagers.

**Expert users** benefit from a significantly simplified and vastly more comprehensible application management workflow. In addition, experts are users and many are are also developers, so they benefit from the aforementioned user and developer benefits too.


## Implementation

### AppImageService
There are several features common to both scenarios that could be implemented together and served by means of a DBUS Service [^first]. This service could be consumed from the file manager or from the software center client.

It will provide the following features:
- launching
- verification
- registration (menu entries creation including icons and mime-types associations)
- update checking and notification
- performing the update
- metadata extraction
- removal

_This service must be considered an enhancement to the AppImage UX but not a mandatory requirement to use AppImages._

[^first]: D-BUS is preferred over a native API (like C/C++) because it doesn't add any build time dependency to the clients.

### AppImageLauncher
In order enforce features such as signature verification and sandboxing, all AppImage launchings must be intercepted the way AppImageLauncher already does. The only difference with the current implementation would be that it will work as a lightweight wrapper that uses the AppImageService in the backend.

### Software Center Client
Software centers may have three interfaces: a CLI, desktop app and a web interface. The CLI and the desktop interface can share most of it's code. They will also consume the AppImageService to realize their features except for the ones related to consulting the software center web service. The web interface will rely on the AppImageLauncher to perform the installation step.

A web service will be required to expose content for the software center clients. The features that such a service must support will be described in another document.

Nevertheless developing such software stres and their corresponding clients is, at the time of writting, out of the scope of the AppImage project as it's cosidered a more feasible solution to extend the existent solutions such as the GNOME Software Center, KDE Discover and others to add AppImages support. Yet we will glad to assist on creating or selecting a common public API specifications.

# Conclusions

**The AppImage Way** represents a significant improvement and simplification for application management on GNU/Linux system. But it's far from being a novel idea in fact the Macintosh System 1.0 ca. 1984 already included such UX. So it's time for GNU/Linux desktop to match it and have applications packed as single files with icons, metadata, launcher integration and update actions right from the file manager.

To make it real, a base service should be created in a way that every distribution can consume it and integrate it as part of their default UX. Keeping the interoperability between the different software components.

The AppImageLauncher project could be used to give users a preview of the purposed workflow and encourage its adoption. But developing Software Centers is out of the project scope. Yet we will assist on the selection or creation of a common public API specification.


# Sources
- https://github.com/AppImage/appimaged/issues/30
- https://github.com/TheAssassin/AppImageLauncher/issues/32
- https://github.com/TheAssassin/AppImageLauncher/issues/102
- https://github.com/TheAssassin/AppImageLauncher/issues/70
- https://phabricator.kde.org/D14998
- https://github.com/AppImage/libappimage/issues/4

# Contributors
- Alexis Lopez
- Nate Graham
- probono
- TheAssassin