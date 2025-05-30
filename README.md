# How-to-Install-Linux-Distro-on-Arm-Chromebook
_Credits: https://wiki.postmarketos.org/wiki/Category:ChromeOS, https://velvet-os.github.io/chromebooks/installation/basic-installation.html_
# Supported Distros (Not very many :L) I found
Ubuntu / Debian : https://github.com/hexdump0815/linux-mainline-on-arm-chromebooks

Cadmium is I THINK (Because I haven't tested it) like Gallium OS but for arm since it supports ARM Chromebooks
Cadmium Specific Instructions : https://github.com/Maccraft123/Cadmium

Postmarket OS: https://wiki.postmarketos.org/wiki/Category:ChromeOS

Two Ways to install Arch Linux

Arch Install Script: https://github.com/altreact/archbk?tab=readme-ov-file

Arch Arm: https://archlinuxarm.org/about/downloads

Fedora Specific Install Instructions

https://fedoraproject.org/wiki/Architectures/ARM/Chromebook

Gentoo Specific Instructions

https://wiki.gentoo.org/wiki/ASUS_Chromebook_C201/Installing_Gentoo#Booting_the_installation_media

Nix OS Specific Instructions
https://github.com/thefloweringash/kevin-nix (Should work fine but it's made for the Samsung Chromebook Plus)

# WARNING
There is absolutely no support for drivers on arm devices by the Chrultrabook Community and Windows Support is reccommended to use UEFI Firmware by the MrChromebox Project which is also not supported by arm, but you may get lucky with installing windows onto a usb.

Also some distros may have specific instructions to them which you can see above these are just general instructions for booting the Distro and copying the files.

# Enabling Dev Mode
Make sure Developer Mode is enabled if not here is how you enable it. (FYI it will clear off all data on the device so make sure everything is backed up)

Press ESC + Refresh + Power

Press CTRL + D

Press Enter

Press CTRL + D

Wait

Press CTRL + D

(Also you'll have to press CTRL + D every time you turn on the chromebook with Chrome OS)

# Enabling USB Boot
Press CTRL + ALT + F2/Top Right Arrow
Login as Chronos
and type in:
`sudo crossystem dev_boot_usb=1 dev_boot_signed_only=0`

And then you can boot into externel storage with CTRL + U when you turn on the chromebook

# Installing onto Chromebook Internel Storage

Root Access
`Sudo -i`

Find Internel S
torage
`lsblk`
the internel storage is almost always mmcblk1, or mmcblk0 in this case we assume it is mmcblk0

Export Disks
`export disk=mmcblk0
export part=mmcblk0p`

Export it (again I don't know why the author of the guide put it twice but ok)
`export srcPart=mmcblk0p`

Wipe the partition table on target disk
`sgdisk -Z /dev/mmcblk0
 partprobe /dev/mmcblk0
 sgdisk -C -e -G /dev/mmcblk0
 partprobe /dev/mmcblk0
`

Create new partiton Table
`cgpt create /dev/mmcblk0`

Create Kernel Partitions
`cgpt add -i 1 -t kernel -b 8192 -s 65536 -l KernelA -S 1 -T 2 -P 10 /dev/mmcblk0
 cgpt add -i 2 -t kernel -b 73728 -s 65536 -l KernelB -S 0 -T 2 -P 5 /dev/mmcblk0`
 
 If you are on ASUS Chromebook Flip user then use
 `cgpt add -i 1 -t kernel -b 73728 -s 32768 -l KernelA -S 1 -T 2 -P 10 /dev/mmcblk0
cgpt repair /dev/mmcblk0
cgpt add -i 2 -t kernel -b 106496 -s 32768 -l KernelB -S 0 -T 2 -P 5 /dev/mmcblk0
cgpt repair /dev/mmcblk0`

Create remaining partitions
`wget https://raw.githubusercontent.com/hexdump0815/imagebuilder/main/systems/chromebook_veyron/gpt-partitions.txt`

`fdisk /dev/mmcblk0 < gpt-partitions.txt`

Create root and boot file systems and mount them
`mkfs -t ext4 -O ^has_journal -m 0 -L bootemmc /dev/mmcblk0p3
mkfs -t btrfs -m single -L rootemmc /dev/mmcblk0p4
mount -o ssd,compress-force=zstd,noatime,nodiratime /dev/mmcblk0p4 /mnt
mkdir -p /mnt/boot
mount /dev/mmcblk0p3 /mnt/boot`

Copy kernel partition to target disk
`dd if=/dev/sdx1 of=/dev/mmcblk0p1 bs=1024k status=progress`

Sync over the boot and root file systems
`rsync -axADHSX --no-inc-recursive --delete /boot/ /mnt/boot
rsync -axADHSX --no-inc-recursive --delete --exclude='/swap/*' / /mnt`

Create Swap File (Optional)
`rm -rf /mnt/swap
btrfs subvolume create /mnt/swap
chmod 755 /mnt/swap
chattr -R +C /mnt/swap
btrfs property set /mnt/swap compression none
cd /mnt/swap
truncate -s 0 ./file.0; btrfs property set ./file.0 compression none; fallocate -l 2G file.0; chmod 600 ./file.0; mkswap -L file.0 ./file.0
`

Do the same in the u-boot config file if it exists (on 32bit arm chromebooks only)
`sed -i 's,bootpart,bootemmc,g;s,rootpart,rootemmc,g' /mnt/etc/fstab `

Unmount everything
`umount /mnt/boot /mnt`


# Credits The Second
https://wiki.postmarketos.org/wiki/Category:ChromeOS
https://velvet-os.github.io/chromebooks/installation/basic-installation.html

Huge thanks to everyone making this unsupported mess possible! It's awesome that there is at least some community around running other OS' on arm chromebooks it's not perfect but it sure as hell is an beauty, but at least consider getting an 86_64 chromebook to try getting other OS' at some point.

Also check out https://velvet-os.github.io/postinst/readme.html to see what you should do next!

