# How-to-Install-Linux-Distro-on-Arm-Chromebook
_Credits: https://wiki.postmarketos.org/wiki/Category:ChromeOS_

Make sure Developer Mode is enabled if not here is how you enable it. (FYI it will clear off all data on the device so make sure everything is backed up)

Press ESC + Refresh + Power
Press CTRL + D
Press Enter
Press CTRL + D
Wait
Press CTRL + D
(Also you'll have to press CTRL + D every time you turn on the chromebook with Chrome OS)

Then press CTRL + ALT + F2/Top Right Arrow
Login as Chronos
and type in:
sudo crossystem dev_boot_usb=1 dev_boot_signed_only=0

And then you can boot into externel storage with CTRL + U when you turn on the chromebook

# WARNING
There is absolutely no support for drivers on arm devices by the Chrultrabook Community and Windows Support is reccommended to use UEFI Firmware by the MrChromebox Project which is also not supported by arm, but you may get lucky with installing windows onto a usb.
