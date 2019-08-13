# **UrBackup Docker FAQ**

**Q1.** I am seeing the message 'error GPT restore' when attempting to restore a system image using UrBackup, what could be the cause of this?

**A1.** if you attempt to restore a system image then the machine you are restoring to (whether vm or physical) must have a disk/vdisk equal to or larger than the machine the backup was taken from, this includes used space AND importantly also free disk space, otherwise you wll receive a 'error GPT restore' when you attempted to restore.
