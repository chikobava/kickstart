# Automated CentOS Stream 10 Installations with Kickstart
This repository demonstrates how to create a Kickstart-enabled installation ISO using **CentOS Stream 10**. The process is streamlined, faster, and less error-prone than traditional methods (e.g. editing grub.cfg, generating iso with mkisofs), because `mkksiso` directly integrates the Kickstart file into the ISO and automates the boot configuration.

**Key Steps:**

1.  Download the CentOS Stream 10 Installation Image.
2.  Prepare Your Kickstart Configuration File.
3.  Install `lorax` image builder tool.
4.  Execute `mkksiso` to generate the custom ISO.

## 1. Download the CentOS Stream 10 Installation Image
To download the CentOS Stream 10 image you can click on the [link](https://mirrormanager.fedoraproject.org/mirrors/CentOS) and choose the mirror closest to you. Then simply pull it with wget.

**Example:** Downloading the boot ISO from a specific mirror to the `/tmp` directory:
```bash
wget https://ftp-chi.osuosl.org/pub/centos-stream/10-stream/BaseOS/x86_64/iso/CentOS-Stream-10-20250512.0-x86_64-boot.iso -O /tmp/centos.iso
```

## 2. Create kickstart file
The Kickstart file (`ks.cfg`) contains all the instructions for the automated installation process, including partitioning, user account setup, package selection, and system configuration. You have two primary ways to create this file:

  * **Generate from an Installed System:** Install a temporary CentOS Stream 10 virtual machine or physical system, configuring all the desired options (disk partitioning, user accounts, timezone, etc.) during the interactive installation. After the installation is complete, the configuration will be saved in the `/root/anaconda-ks.cfg` file. You can then copy this file to your working directory and modify it as needed. If you choose this method, do not forget to replace `graphical` installation mode with `text`, this small change will significantly increase the installation speed.

  * **Use a Pre-existing Template:** This repository includes a basic Kickstart configuration file (`ks/ks.cfg`). This template provides a starting point with configurations for enhanced `sshd` security. You can copy your public SSH key and set passwords for the `root` and an `admin` user. Copy this file to `/tmp/ks.cfg` and carefully review and customize it to match your specific requirements.

**Note:** Remember to edit `ks.cfg` and replace placeholder values with your actual desired settings, as indicated by the comments within the file.

You can validate your ks file using ksvalidator:
```bash
dnf install pykickstart
ksvalidator -v RHEL10 ks.cfg
```

## 3. Install the `lorax` image builder
`lorax` is a powerful utility used to generate bootable installation images and media for Linux distributions. We will leverage it to create a customized ISO image incorporating your Kickstart file.

To install `lorax` on a RPM-based system (including CentOS Stream):
```bash
sudo dnf install lorax
```

## 4. Execute `mkksiso` to Generate the Custom ISO
The final step involves using the `mkksiso` command, which is part of the `lorax` suite, to combine your Kickstart file with the original CentOS Stream 10 ISO image, creating a new, bootable ISO that will automatically initiate the installation using your preconfigured settings.

The general syntax for `mkksiso` is:
```bash
mkksiso --ks /PATH/TO/KICKSTART /PATH/TO/ISO /PATH/TO/NEW-ISO
```
Based on the file paths we've used in the previous steps:
```bash
mkksiso --ks /tmp/ks.cfg /tmp/centos.iso /tmp/custom.iso
```
After executing this command, `mkksiso` will process the files and generate your custom ISO image (`/tmp/custom.iso`). This process might take a few moments depending on your system's resources and the size of the base image.

Once the process is complete, you can use the `custom.iso` file to perform automated installations of CentOS Stream 10 on your servers or virtual machines. Simply boot from this ISO, and the installation will proceed unattended according to the specifications in your Kickstart file.

Happy automated kickstarting!