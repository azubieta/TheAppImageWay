# The AppImage Way

This project attempts to bring the applications management user experience of the *Macintosh 
System 1.0 ca. 1984* into modern GNU/Linux system. Which features **single file applications**, 
**transparent system integration** (no install required) and the **applications directory**.

This project follows a well defined set of specifications that can be found in the 
[SPECIFICATIONS FILE](https://github.com/azubieta/TheAppImageWay/blob/master/SPECIFICATION.md).

To realize this user experience were created a set of tools:
- [appimage-services](https://github.com/azubieta/AppImageServices) core integration service that ensures interoperability
 between tools and a solid user experience.
- [appimage-firstrun](https://github.com/azubieta/appimage-firstrun) first run assistant to ease system integration and applications inspection
- [appimage-appsdir](https://github.com/azubieta/appimage-appsdir) applications directory monitor and back-end applications 
registration tool

There are also desktop environment specific tools thant enhances even more user experience
by providing file thumbnails, file metadata, native progress dialogs and more:
- [kde plasma appimage integration](https://github.com/azubieta/plasma-appimage-integration)
- [gnome thumbnailer](https://github.com/azubieta/appimage-gnome-thumbnailer)



**Installation instructions**

The following script will allow you to install the whole AppImage user experience on your 
system. It assumes that you have a modern GNU/Linux system (glib >= 2.40) with `systemd` 
and `wget` installed. 
 
```shell script
echo "Downloading tools"
wget https://github.com/azubieta/AppImageServices/releases/download/continuous/appimage-services-x86_64.AppImage -O /user/local/bin/appimage-services
wget https://github.com/azubieta/appimage-appsdir/releases/download/continuous/appimage-appsdir-x86_64.AppImage -O /usr/local/bin/appimage-appsdir
wget https://github.com/azubieta/appimage-firstrun/releases/download/continuous/appimage-firstrun-x86_64.AppImage -O /usr/local/bin/appimage-firstrun

chmod a+x /usr/local/bin//appimage-*
ln -s /usr/local/bin/appimage-firstrun /usr/bin/appimage-firstrun

echo "installing services"
sudo appimage-services self-install
sudo appimage-appsdir self-install
```

_Non-root installation is possible but the `binfmt` handler will not be available. Making
impossible to show the first run assistant if the AppImage files are already executable._

**Collaboration & Error Reports**

This is an open project and every one is welcome to contribute. If you have an idea on how
to improve the user experience or the tools please fell free to open an issue or send a pull
request. We will take care of it ASAP.

**Support**

You can contribute to the project not only with code but also with coffee or tea. You can 
use [**this link**](https://www.paypal.com/paypalme2/azubieta) to buy my a cup.