# **UrBackup Docker FAQ**

**Q1.** I am seeing the message 'error GPT restore' when attempting to restore a system image using UrBackup. What could be the cause of this?

**A1.** If you attempt to restore a system image, then the machine you are restoring to (whether VM or physical) must have a disk/vdisk equal to or larger than the machine the backup was taken from. This includes used space AND, importantly, also free disk space; otherwise, you will receive an 'error GPT restore' when you attempt to restore.