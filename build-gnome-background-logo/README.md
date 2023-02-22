# build-gnome-background-logo
Building the GNOME Shell "Background Logo" extension from Fedora on deb-based Linux distributions

## Prequisites
- a PC running a "deb" type Linux distribution with the GNOME desktop as default (Debian, Ubuntu, Pop!_OS, etc.)
- `sudo` permissions on the command line (`sudo <command>`)
- an internet connection

## 1. Update, upgrade, and install build packages
- Open a terminal and issue the following commands in order:
        `sudo apt update`  
        `sudo apt upgrade -y`  
        `sudo apt install gnome-tweaks gnome-shell-extension-manager meson ninja-build`  
- `gnome-tweaks` ("Tweaks") is a popular application that allows additional configuration of your GNOME desktop environment outside of the default Settings menu.
- `gnome-shell-extension-manager` ("Extension Manager") is a local application that allows for management of installed GNOME Shell Extensions, and also allows you to browse for and install extensions without having to use the website and install the "GNOME Shell integration".
- `meson` and `ninja-build` are command-line build systems focused on speed, reliability, and ease of use.

## 2. Prepare your build environment and clone the source code
- Open your Settings menu and scroll down until you see "About"; click on "About" and take note of your "GNOME Version" (in my case, "42.5").
- In the terminal, create a "temp" directory; for my example, I will create a "hidden" directory called "tmp" in my home directory: `mkdir ~/.tmp`
- Change directories into the newly-created ".tmp" folder: `cd ~/.tmp`
- Clone the source code for the "gnome-shell-extension-background-logo" using the corresponding MAJOR VERSION number for GNOME (for example, if your GNOME Version is 42.5, use the "42.0" version); you can view the available builds [here](https://releases.pagure.org/background-logo-extension/): `wget https://releases.pagure.org/background-logo-extension/background-logo-extension-42.0.tar.xz`
- Extract the source code: `tar -xvf background-logo-extension-42.0.tar.xz`

## 3. Compile and build the extension
- In the terminal, from the ~/.tmp directory, change directories into the extracted source code directory (your folder name may be slightly different depending on your GNOME version and the source code you cloned in Step 2): `cd background-logo-extension-42.0.tar.xz`
- Verify the contents of the source code directory with `ls -a`; it should match the below:
        ```
        .        .eslintrc.json  lint                  metadata.json.in  schemas  
        ..       extension.js    meson.build           NEWS  
        COPYING  .gitignore      meson-postinstall.sh  prefs.js  
        ```
- Compile and build the extension to a "build" directory: `meson build`
- Re-verify the directory's contents with `ls -a`; there should now be a "build" directory listed:
        ```
        .      COPYING         .gitignore   meson-postinstall.sh  prefs.js  
        ..     .eslintrc.json  lint         metadata.json.in      schemas  
        build  extension.js    meson.build  NEWS  
        ```
- Install the extension from the build directory: `sudo ninja -C "build" install`
- Reboot your PC or log out and log back in to re-poll the extensions (I personally recommend reboot).

## 4. Enable and configure the background-logo extension
- Open "Extension Manager" from your installed applications.
- Verify under "System Extensions" that "Background Logo" is now listed; toggle it on.
- Click the gear icon to bring up the configuration menu.
- Scroll down to "Options" and enable "Show for all backgrounds".
- If you are running in Light mode, click on the "Filename" field to browse to your desired image; if you are running in Dark mode, click on "Filename (dark)"; I recommend a Scalable Vector Graphic (.svg) to avoid pixelation of the image at larger sizes.