+++
author = "Oscar Costa"
title = "Fedora 38 After Install Guide - 2023 Edition"
date = "2023-07-02"
description = "Fedora 38 After Install Guide - 2023 Edition"
tags = [
    "blog", "linux"
]
+++

After using Fedora Linux on my Dell Inspiron 5515 laptop with an AMD processor for 2 years and only updating the system, I decided to perform a fresh installation. This was to clean up unused applications and attempt to fix some strange behaviors I had been experiencing. This post serves as a reminder to myself on what to do after installing Fedora Linux from scratch on my working machine.

This post consists of sections that list how to perform **System Update and Configuration**, as well as how to install **Additional Packages**, **Gnome Extensions**, and **Other Software** that I use on a daily basis.

-----

## 1. System Update and Configuration

Update the system using **Gnome Software** or via the terminal using the command `$ sudo dnf update`. It may be necessary to restart the system to update some packages.

Enable the Third Party and RPM Fusion repositories, and update the system once more. Some of these repositories may already be enabled during installation.

```bash
$ sudo dnf install fedora-workstation-repositories
$ sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
$ sudo dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
$ sudo dnf update
```

*Reference: https://docs.fedoraproject.org/en-US/quick-docs/setup_rpmfusion/*

### Additional Configs

**Auto-mount second disk**

I have a second encrypted SSD in which I store VM images, home folder backups, and other things. This SSD has to be automatically mounted. To configure the system to auto-mount this second SSD, we need to:

1. Create a folder to serve as the mount point, e.g: `/mn/data/`.
2. Open the GNOME Disks application.
3. Locate the SSD in the list of drives, and mount it if necessary.
4. Select the encrypted LUKS partition and access its *Encryption Options*.
5. In the Encryption Options dialog, disable the defaults option.
6. Select the decrypted partition and access its *Mount Options* 
7. In the Mount Options dialog, disable the defaults option and make the necessary changes. For example, specify the desired mount point (e.g., /mnt/data/).
8. Take ownership of the drive by configuring the appropriate settings within the GNOME Disks application.

*Reference: https://www.zdnet.com/article/how-to-automount-a-drive-in-linux-the-gui-way-with-gnome/*

**Scale external display**

My laptop has a 15.6" display with a 1080p resolution, and the default 100% scale works great for me. However, when using an external 4k display, the default scales of 100% and 200% make things either too small or too big. To solve this issue without lowering the monitor resolution, we need to allow other display scales. To do that, we need to enable the experimental feature "scale-monitor-framebuffer" using **gsettings**.

```bash
$ gsettings set org.gnome.mutter experimental-features "['scale-monitor-framebuffer']"
```

Alternatively, this change can be made using **dconf Editor**. After applying the feature, it is possible to select scale values like 125%, 150%, 175%, and others in the display settings for each monitor.

*Reference: https://wiki.archlinux.org/title/HiDPI*

-----
## 2. Additional Packages & Applications

Most of the packages listed below are present in the **Gnome Software** application. However, some of them are available only via Flatpak. Since I prefer using [**DNF**](https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/package-management/DNF/) over [**Flatpak**](https://docs.fedoraproject.org/en-US/flatpak/), those packages that are not available via DNF require a few more tweaks to install.

Moreover, for some of the applications bellow, I prefer to run them from the user folder. So, I simply download and run them as it is. Some of the applications based on AppImage or Electron automatically install themselfes in the current user folder and create application shortcuts or desktop entries.

### 2.1 Gnome Applications

**GNOME Extensions**: You can install GNOME Extensions by searching for "Extensions" in Gnome Software or by using the terminal command: `$ sudo dnf install gnome-extensions`. Alternatively, you can manage Gnome Extensions directly from the website using the browser extension GNOME Shell Integration.

*Reference: https://extensions.gnome.org/*

**GNOME Tweaks**: To install GNOME Tweaks, look for "Tweaks" in Gnome Software or use the terminal command: `$ sudo dnf install gnome-tweaks`.

*Reference: https://wiki.gnome.org/Apps/Tweaks*

**Dconf Editor**: You can install Dconf Editor by searching for "Dconf Editor" in Gnome Software or using the command: `$ sudo dnf install dconf-editor`. 

*Reference: https://wiki.gnome.org/Apps/DconfEditor*

**Pomodoro**: Previously available through Gnome Extensions, Pomodoro is now included in the Fedora repositories. To install it, use Gnome Software or the terminal command: `$ sudo dnf install gnome-pomodoro`. After installation, the Pomodoro extension will be enabled on the next login.

*Reference: https://gnomepomodoro.org/*

**PulseAudio Volume Control**: Install PulseAudio Volume Control using Gnome Software or the terminal command: `$ sudo dnf install pavucontrol`.

*Reference: https://fedoramagazine.org/precise-audio-control-with-fedora/*

### 2.2 Photo/Image Editing

**Darktable**: Install it via Gnome Sofware.

**GNU Image Manipulation Program (Gimp)**: Install it via Gnome Sofware.

### 2.3 Compression utilities

Install via terminal.

```bash
$ sudo dnf install -y unzip p7zip p7zip-plugins unrar 
```

### 2.4 Communication

**Discord**: It can be easily installed from the RPM-fusion non-free repository using the command: `$ sudo dnf install discord`

*Reference: https://itsfoss.com/install-discord-fedora/*

**Skype**: To install Skype on Fedora, you need to add the Skype repository and then install the Skype package:

```bash
$ sudo curl -o /etc/yum.repos.d/skype-stable.repo https://repo.skype.com/rpm/stable/skype-stable.repo
$ sudo dnf install skypeforlinux
```

*Reference: https://docs.fedoraproject.org/en-US/quick-docs/installing-skype/*

### 2.5 Browsers

**Google Chrome**: Enable Google Chrome repo and install the stable Chrome package.

```bash
$ sudo dnf config-manager --set-enabled google-chrome
$ sudo dnf install google-chrome-stable
```

*Reference: https://docs.fedoraproject.org/en-US/quick-docs/installing-chromium-or-google-chrome-browsers/#installing-chrome*

**Firefox Developer**: 

1. Download the tar.bz file from the official Mozilla website https://www.mozilla.org/en-US/firefox/developer/ and extract it. 

2. Create a desktop entry for easier access by creating a `.desktop` file, e.g. `firefoxDeveloper.desktop`, in the `/home/$USER/.local/share/applications/` directory with the following content:

```bash
[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Name=Firefox Developer
GenericName=Firefox Developer
Comment=Firefox Browser Developer Edition
Exec=/path/to/firefox-developer-edition/firefox
Icon=/path/to/firefox-developer-edition/browser/chrome/icons/default/default128.png
Categories=Development;
```

*Note: Replace `/path/to/firefox-developer-edition/` with the actual path where you extracted Firefox Developer Edition.*

Now you can find and launch Firefox Developer Edition from your applications menu.

**Tor Browser**: 

1. Download the tar.xz file from the official Tor Project website https://www.torproject.org/download/ and extract it. 

2. Run the script `star-tor-browser` located in the extracted directory. 

3. Copy the `start-tor-browser.desktop` file to `/home/$USER/.local/share/applications/`.

Now you can launch Tor Browser from your applications menu.

### 2.6 Cloud Backup

**iDrive For Linux**: Download zip file from https://www.idrive.com/online-backup-linux-download and follow the installation instructions in https://www.idrive.com/faq_linux#linuxWeb4.

**pCloud**: Download the installer from https://www.pcloud.com/download-free-online-cloud-file-storage.html and run the downloaded file.

**Dropbox**: To install Dropbox on Fedora:

1. In the terminal, create a repo for Dropbox.

```bash
$ cat <<EOF | sudo tee /etc/yum.repos.d/dropbox.repo 
[dropbox]
name=Dropbox
baseurl=https://linux.dropbox.com/fedora/$releasever/$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://linux.dropbox.com/fedora/rpm-public-key.asc
EOF
```

2. Install Dropbox using DNF `$ sudo dnf install dropbox`

Alternatively, you can download the Fedora RPM package from the Dropbox website (https://www.dropbox.com/install-linux) and install it by double-clicking on the downloaded file or running the RPM command in the terminal:

```bash
$ sudo rpm -i nautilus-dropbox-2020.03.04-1.fedora.x86_64.rpm
```

If RPM reports any missing dependencies, install them using DNF.

### 2.7 Development tools

**hugo**: Install it using DNF ```$ sudo dnf install hugo```

**vim**: Install it using DNF ```$ sudo dnf install vim```

**Java**: To install Java on Fedora, you can use the OpenJDK packages available in the repositories.

1. Search for available OpenJDK versions using DNF: `$ sudo dnf search openjdk`

2. Install the desired OpenJDK version. For example, to install OpenJDK 17: `$ sudo dnf install java-17-openjdk`

3. If you need the development or other files, install the corresponding OpenJDK development package: `$ sudo dnf install java-17-openjdk-devel`

*Reference: https://itsfoss.com/install-java-fedora/*

**JetBrains ToolBox**: 

1. Download the JetBrains Toolbox tar.gz package from the official website https://www.jetbrains.com/toolbox-app/.

2. Extract the downloaded tar.gz package

3. Run the JetBrains Toolbox AppImage.

*Note: Ensure you have the necessary dependencies installed to run the AppImage, such as libglib2.0-0 and libgtk-3-0. If any dependencies are missing, you may need to install them using the package manager (DNF) before running the AppImage.*

**VSCode**: 

1. Download the VSCode tar.gz package from the official website https://code.visualstudio.com/Download.

2. Extract the downloaded package.

3. If necessary, create a desktop entry in `/home/$USER/.local/share/applications/vscode.desktop` with the following content:

```bash
[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Name=VS Code
GenericName=VS Code
Comment=Visual Studio Code - Code Editing. Redefined
Path=/path/to/vscode/
Exec=/path/to/vscode/./code
Icon=/path/to/vscode/resources/app/resources/linux/code.png
Categories=Development;IDE;
WM_CLASS=VS Code
StartupWMClass=VS Code
StartupNotify=true
```

*Note: Replace `/path/to/vscode/` with the actual path where you extracted VSCode.*

Now you can launch VSCode from your applications menu.

**Node.js**: Download and run the nvm install script:

```bash
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

Then, install Node.js using nvm: `$ nvm install v18`

*Reference: https://github.com/nvm-sh/nvm#installing-and-updating*

### 2.8 Other Applications

**Balena Etcher**: Download the AppImage from https://etcher.balena.io/ and run it.

**Calibre**: Install it via **Gnome Sofware**.

**NordVPN**: Download and run NordVPN install script. This script add the NordVPN repo and install the nordvpn package. 

```bash
$ sh <(curl -sSf https://downloads.nordcdn.com/apps/linux/install.sh)
```

Then, add the current user to the nordvpn group `$ sudo usermod -aG nordvpn $USER` , and log off to be able to use NordVPN.

*Reference: https://support.nordvpn.com/Connectivity/Linux/1325529112/Installing-and-using-NordVPN-on-Fedora-and-QubesOS-Linux.htm*

**Remmina**: Install it via **Gnome Sofware**.

*Reference: https://remmina.org/how-to-install-remmina/#fedora-and-red-hat*

**VirtualBox**:

1. Register the VirtualBox repo: 

```bash
$ sudo wget https://download.virtualbox.org/virtualbox/rpm/fedora/virtualbox.repo -O /etc/yum.repos.d/virtualbox.rep
```

2. Install the latest VirtualBox package: `$ sudo dnf install VirtualBox-7.0`

3. Add the current user to *vboxusers* group: `$ sudo usermod -aG vboxusers $USER`

*Reference*
*- https://computingforgeeks.com/how-to-install-virtualbox-on-fedora-linux/* 

-----
## 3. Gnome Extensions

Here is a list of Gnome extensions that I currently use. You can install them easily using the **GNOME Shell integration** browser extension or by downloading them from https://extensions.gnome.org/ and opening them with the **GNOME Extensions** application.

- **Draw On Your Screen 2** - Allows you to draw and annotate on your screen. Get it from: https://extensions.gnome.org/extension/4937/draw-on-you-screen-2/

- **gTile** - Provides advanced window tiling options to customize your workspace. Get it from: https://extensions.gnome.org/extension/28/gtile/

- **Tray Icons: Reloaded** - Brings back tray icons to the top bar. Get it from: https://extensions.gnome.org/extension/2890/tray-icons-reloaded/

- **Vitals** - Displays system resource information in the top bar. Get it from: https://extensions.gnome.org/extension/1460/vitals/

Note: Make sure to check compatibility with your current version of GNOME before installing these extensions.

-----
## 4. Conclusions

This is definitely not an extensive and final guide on what to do after a fresh install of Fedora Linux. It is tailored to my personal preferences and the specific hardware and software I use on a daily basis. However, I hope that some of the tips and recommendations provided in this guide will be helpful to others as well.

*Disclaimer: The information and recommendations provided in this guide are based on the author's personal experiences and preferences. The author is not affiliated with any of the mentioned software or organizations, and the recommendations are not endorsements. Users should exercise their own discretion and refer to official documentation and sources for the latest information and instructions.*
