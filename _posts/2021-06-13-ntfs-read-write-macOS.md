---
layout: post
title: Make NTFS USB Flashes read and write in macOS
subtitle: A simple script to make NTFS USB Flashes read and write in macOS without reformatting it
tags: [NTFS, macOS, python]
readtime: true
---

I had an issue when I first used my new MacBook Pro. I had Yosemite installed then.

I wanted to get some data from my old PC. The regular way is copying them using a flash driver and paste them into the specified location.

But what if you want to copy data from the MBP to some other device using a flash driver?  
I found that the macOS doesn't allow the users to write to the NTFS flash drivers, It was mounting the driver in read-only mode and what I wanted is to be mounted in read-write mode, so I had to search about a way to do that. I found so tools but they were not free to use and I had to buy one of them to achieve what I wanted to do. I think I bought some one of them but really don't remember what was it.

Then I decided to search about how to fix it using the code _**as I'm a software engineer in the first place**_ and I found a way to do that.

#### Here is a simple **python** script that do the following: 
1. Lists the available disks in your macOS
2. Asks you if you see your NTFS flash driver
3. Then, if you see it will ask you to enter its name to mount it to be used as a normal flash driver
4. It will use a tool called `ntfs-3g` [_read more about it_](https://github.com/osxfuse/osxfuse/wiki/NTFS-3G), it can be installed using Home-brew `brew install ntfs-3g`
5. Finally, it will mount the NTFS volumes in read-write mode

All you need to do to have the following script working is:
1. Make sure that you have python installed, it can be installed using Home-brew also `brew install python`
2. Copy the script to anywhere in your macOS.
3. Plugin the NTFS flash driver.
3. Run it `python <THE-NAME-OF-THE-SCRIPT>.py`

```python
import subprocess

# helper subprocess method
def exec_cmd(command):
    subprocess.call(command, shell=True)

def ntfs_3g_mounter():
    # get all the info about the disks
    exec_cmd('df')
    # make sure that the disk is unmounted if it's amounted
    disk_exist = input('Do you see the disk you want to amount?[Y/n]')
    if disk_exist.lower() == 'y':
        disk_name = input('what\'s the name of the disk you want to amount?')
        if len(disk_name.strip()) > 0:
            # TODO: need to be improved.
            # make sure it's unmounted
            exec_cmd('diskutil unmount {disk}'.format(disk=disk_name))
            # create a new folder under /Volumes
            exec_cmd('sudo mkdir /Volumes/NTFS')
            # mount this disk into the newly created folder
            command = 'sudo ntfs-3g /dev/{disk} /Volumes/NTFS -olocal -oallow_other'.format(disk=disk_name)
            exec_cmd(command)
        else:
            print('Invalid disk name')
            exit()
    elif disk_exist.lower() == 'n':
        print('Unplug it and plug it again.')
        start_again = input('Do you want to try again?[Y/n]')
        if start_again.lower() == 'y':
            ntfs_3g_mounter()
        elif start_again.lower() == 'n':
            exit()

ntfs_3g_mounter()
```