# Kickstart

This repository contains a few simple example files to demonstrate how to create a kickstart image using Centos 7.

Steps to produce a kickstart image:

* Download Centos 7 image.
* Create kickstart file (see ./configuration_files/ks.ssh.cfg for an example).

**Note**: please replace variables enclosed in ${} with corresponding values, see comments in the ks.cfg

* Unpack the Centos 7 image to some folder (for example `/tmp/centos`).

For the purposes of this tutorial let's assume, you've unpacked your centos7.iso into /tmp/centos.

* Copy your kickstart file to /tmp/centos/ks/ks.cfg
* Edit grub

To edit grub for [efiboot](https://bbs.archlinux.org/viewtopic.php?id=240034#:~:text=the%20live%20DVD%3F-,efiboot.,CD%2FDVD%20type%20installation%20medias.), you will need to mount (as writable) the .img file in `/tmp/centos/images/efiboot.img`.<br> Then you will need to edit the EFI/BOOT/grub.cfg and add your options there. You can use the grub.cfg in configuration_files as a reference.
You can also amend the [isolinux](https://wiki.syslinux.org/wiki/index.php?title=ISOLINUX) file in /tmp/centos/isolinux/isolinux.cfg.

* Change to /tmp/
Execute the following command:

```bash
mkisofs -U -r -v -T -J -joliet-long \
    -V "CentOS 7 x86_64" \
    -volset "CentOS 7 x86_64" \
    -A "CentOS 7 x86_64" \
    -b isolinux/isolinux.bin \
    -c isolinux/boot.cat \
    -no-emul-boot -boot-load-size 4 -boot-info-table -eltorito-alt-boot \
    -e images/efiboot.img \
    -no-emul-boot \
    -o custom.iso centos/
```

Here is breakdown of the command:

* `-U`: Generate a 32-bit UFS filesystem.
* `-r`: Use Rock Ridge extensions to support long filenames and POSIX permissions.
* `-v`: Verbose mode, which displays detailed output during the creation process.
* `-T`: Translates file names to uppercase.
* `-J`: Generate Joliet directory records for Windows compatibility.
* `-joliet-long`: Enable Joliet long file name support.
* `-V "CentOS 7 x86_64"`: Specify the volume ID of the ISO image.
* `-volset "CentOS 7 x86_64"`: Set the volume set ID of the ISO image.
* `-A "CentOS 7 x86_64"`: Set the application ID of the ISO image.
* `-b isolinux/isolinux.bin`: Specify the path to the boot image.
* `-c isolinux/boot.cat`: Specify the path to the boot catalog.
* `-no-emul-boot`: Disable emulation of boot image.
* `-boot-load-size 4`: Specify the boot image load segment size.
* `-boot-info-table`: Generate boot information table.
* `-eltorito-alt-boot`: Specify an alternative boot image.
* `-e images/efiboot.img`: Specify the path to the EFI boot image.
* `-o custom.iso`: Specify the output file name as "custom.iso".
* `centos/`: The source directory containing the files to be included in the ISO image.

The produced image will be in /tmp/custom.iso