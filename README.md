# TH Guest computer
This repository contains all files needed to set up/customize the guest computer at [Schloss Tempelhof](https://www.schloss-tempelhof.de/).

The guest user will be freshly created on every boot and deleted on every shut down to prevent leaving personal information. The user account gets customized.

## Usage
### Preparation
1. Install a current Ubuntu LTS Desktop edition (at time of writing: Ubuntu 22.04)
  1. Follow graphical installer to set hostname, timezone, etc according to your needs
  2. Create an administrative user `administrator` during installation
    *Note:* if you want to choose a different user name, you have to rename the file `var/lib/AccountsService/users/administrator` accordingly.
2. Install additional software on the computer: `sudo apt-get install gimp gimp-help-de brasero vcdimager gstreamer1.0-plugins-bad nautilus-image-converter vlc libdvdcss2`

### Provisioning
*Note:* As only a single computer is used, the process was not automted by. e.g. [Ansible](https://en.wikipedia.org/wiki/Ansible_(software)), but doing so should be trivial.
1. Clone the repository
2. Change the password of the guest user in file `/usr/local/sbin/add-guest-user.sh`
   *Note:* If you additionally want to change the username, do so in following files:
  1. `/usr/local/sbin/add-guest-user.sh`
  2. `/usr/local/sbin/delete-guest-user.sh`
  3. `/usr/local/sbin/daily-shutdown.sh`
  4. `/etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_hibernate.pkla`
  5. `/etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_suspend.pkla`
  6. `/etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_lock_sessions.pkla`
3. Copy the files over to their final destionation on the guest computer\
   *Note:* If the file already exists (e.g. `/etc/crontab`) add its content to the existing file.\
   *Note:* Double check that file permissions fit.

## Features
### Automatic updates
*Reason:* Run (not only security) updates automatically in background to bother guest user as little as possible\
*Source:* [https://wiki.ubuntuusers.de/Aktualisierungen/Konfiguration/]
*File:* [`/etc/apt/apt.conf.d/50unattended-upgrades`](/etc/apt/apt.conf.d/50unattended-upgrades)

### Remove administrator user from user list at logon screen
*Reason:* Don't wake sleeping dogs\
*Source:* [https://askubuntu.com/questions/92349/how-do-i-hide-a-particular-user-from-the-login-screen]\
*File:* [`/var/lib/AccountsService/users/administrator`](/var/lib/AccountsService/users/administrator)

### Prevent suspend, hibernate, log-off for guest user
*Reason:* After every usage a restart is intended, to ensure deletion of all personal data\
*Source:*
  - [https://wiki.ubuntuusers.de/PolicyKit/]
  - [https://askubuntu.com/questions/972114/ubuntu-17-10-cant-disable-suspend-with-systemd-hybrid-sleep]
  - [https://sites.google.com/site/easytipsforlinux/disable-hibernate-and-suspend]
  - [https://askubuntu.com/questions/93542/how-to-disable-shutdown-reboot-suspend-hibernate]
  - [https://www.freedesktop.org/software/polkit/docs/0.105/pklocalauthority.8.html]

*Files:*
  - [`/etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_hibernate.pkla`](etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_hibernate.pkla)
  - [`/etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_suspend.pkla`](etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_suspend.pkla)
  - [`/etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_lock_sessions.pkla`](etc/polkit-1/localauthority/20-org.d/de.schloss_tempelhof.disable_lock_sessions.pkla)
  - [`/usr/local/sbin/customize-guest-user.sh`](usr/local/sbin/customize-guest-user.sh)\
*Note:* Preventing to log off (without to shut down) seems to *not* work

### Forced restart every nigth (with warning dialogue beforehand)
*Reason:* After every usage a restart is intended, to ensure deletion of all personal data\
*Source:* 
[https://askubuntu.com/questions/567955/automatic-shutdown-at-specified-times]
[https://serverfault.com/questions/229021/how-to-use-crontab-to-display-something-to-users-on-display-0-0-or-run-a-gui-pr]
[https://wiki.ubuntuusers.de/Zenity/]
*Files:* 
  - [`/usr/local/sbin/daily-shutdown.sh`](/usr/local/sbin/daily-shutdown.sh)
  - [`/etc/crontab`](/etc/crontab)
  - [`/usr/local/sbin/customize-guest-user.sh`](/usr/local/sbin/customize-guest-user.sh)

### Create guest user during boot and delete it during shut down
*Reason:* Every guest shall start with an empty user profile, no personal data shall be left over\
*Source:* 
  - [https://stackoverflow.com/questions/2150882/how-to-automatically-add-user-account-and-password-with-a-bash-script]
  - [https://unix.stackexchange.com/questions/57796/how-can-i-assign-an-initial-default-password-to-a-user-in-linux/57806#57806]
  - [https://askubuntu.com/questions/83532/how-do-i-set-up-new-users-with-skel]
  - [https://askubuntu.com/questions/420784/what-do-the-disabled-login-and-gecos-options-of-adduser-command-stand]
  - [https://de.wikipedia.org/wiki/GECOS-Feld]
  - [https://askubuntu.com/questions/814/how-to-run-scripts-on-start-up]
  - [https://askubuntu.com/questions/293312/execute-a-script-upon-logout-reboot-shutdown-in-ubuntu]
  - [https://askubuntu.com/questions/1152179/how-can-i-disable-the-connect-your-online-accounts-dialog-from-the-command-li]
  - [https://askubuntu.com/questions/1028822/disable-the-new-ubuntu-18-04-welcome-screen]

*Files:* 
  - [`/usr/local/sbin/add-guest-user.sh`](/usr/local/sbin/add-guest-user.sh)
  - [`/usr/local/sbin/delete-guest-user.sh`](/usr/local/sbin/delete-guest-user.sh)
  - [`/etc/systemd/system/add-and-delete-guest-user.service`](/etc/systemd/system/add-and-delete-guest-user.service)

### Customize guest user
*Reason:* Provide Tempelhof branding, help and limit rights of user\
*Source:* 
  - [https://askubuntu.com/questions/270049/how-to-run-a-command-at-login]
  - [https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html]
  - [https://askubuntu.com/questions/66914/how-to-change-desktop-background-from-command-line-in-unity]
*Files:* 
  - [`/usr/local/de.schloss_tempelhof.UserCustomization.desktop`](/usr/local/de.schloss_tempelhof.UserCustomization.desktop)
  - [`/usr/local/sbin/customize-guest-user.sh`](/usr/local/sbin/customize-guest-user.sh)
  - [`/usr/local/background.png`](/usr/local/background.png)\
*Note:* Due a bug (potentially in [Mutter](https://gitlab.gnome.org/GNOME/mutter)) in Ubuntu 22.04 ist is not possible to set a background color (other than black) in parallel to using a background image.

### Customize Firefox
*Reason:* Provide Tempelhof branding and limit rights of user\
*Source:* 
  - [https://support.mozilla.org/de/kb/firefox-mithilfe-der-datei-policiesjson-anpassen]
  - [https://github.com/mozilla/policy-templates/blob/master/README.md]
  - [https://addons.mozilla.org/en-US/firefox/addon/enterprise-policy-generator/]\
*File:* [`/etc/firefox/policies/policies.json`](/etc/firefox/policies/policies.json)\
*Note:* Customization is valid for all users!

## License
Feel free to contribute and use the files under following license:

    TH guest user
    Copyright (C) 2020  sjjh

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <https://www.gnu.org/licenses/>.
