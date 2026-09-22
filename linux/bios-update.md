## Check your computer model

On Arch go to `/sys/class/dmi/id` where you can check the following:

1. **product_name** - the name of your computer model
2. **bios_version**
3. **bios_date**

In my case it is `NUC13ANKI7`.

[Here](https://www.asus.com/supportonly/nuc13anki7/helpdesk_bios/) you can download the BIOS firmware after selecting your model name.

Download the zip file, unzip it and in the `Capsule File for BIOS Flash through F7` you can find the `*.CAP` file.  
Move this file to the root of the prepared USB drive.

## Prepare the USB drive

- using `lsblock -f` check the USB drive name and partitions
- format the USB drive partition to FAT32

### Removing old partitions and creating new ones

You can use an interactive tool `cfdisk` on Arch or `fdisk` utility (see separate note).

```bash
# use the block name after running lsblock
sudo cfdisk /dev/sda
```

- delete or create new partitions/partition tables
- choose the correct type of the new partition table (choose `MBR`, or `dos`)

### Formatting the disk partition

- on Arch download the `dosfstools` utility

```bash
# use sda1 or other name after running lsblock
sudo mkfs.vfat -F 32 -n "BIOS_UPDATE" /dev/sda1
```

Then mount the USB drive and copy the `.CAP` file to this drive.  
Turn off the computer.  
Stick the USB drive into the computer, turn it on and press the `F7` key.  
Choose the drive and the `CAP` file.  
Don't turn the computer on and wait until it reboots (no need do pull out the USB drive).
