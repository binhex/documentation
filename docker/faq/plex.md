# **Plex Docker FAQ**

**Q1.** How do i configure Plex to transcode to RAM/Array/Cache?

**A1.** Below are the different options for setting transcoding for Plex and Plex Pass:-

**Transcode to RAM** - create a new volume mapping, host path /tmp and container path /transcode then define TRANS_DIR so that it points at ram drive e.g.:-

```TRANS_DIR=/transcode```

```/transcode```

maps to host path

```/tmp```

**Transcode to the array** - create a new unRAID user share named 'Transcode' (or whatever you want) and then define TRANS_DIR so that it points at your array e.g.:-

```TRANS_DIR=/transcode```

```/transcode```

maps to host path

```/mnt/user/Transcode```

**Transcode to cache** (preferably cache is SSD not spinner) - define TRANS_DIR so that it points at your cache drive e.g.:-

```TRANS_DIR=/transcode```

```/transcode```

maps to host path

```/config/tmp```

**Note**:- Recommended transcode method is to use the cache drive.

**Q2.** How do i configure Plex to use my GPU for encoding/decoding (sometimes referred to as hardware transcoding)?

**A2.** The best way to see how to achieve this is by watching the excellent YouTube video by SpaceInvader One, link below:-

https://www.youtube.com/watch?v=GOhHiFAXwOE

**Q3.** I've been running Plex Media Server on unRAID ver 6.7.x for some time, now all of a sudden it won't start/has stopped working/won't index new content, what is the cause?.

**A3.** If you are **running unRAID 6.7.x AND store your Plex metadata on the array** (don't use or have a cache drive) then there is an issue in regards to Sqlite Databases, where you will get random database corruption, causing Plex to either completely stop working or be unable to playback or scan in new content. 

The best way to diagnose if this is the issue is by looking at the Plex log, this is stored in the following location:-

```
/config/Plex Media Server/Logs/Plex Media Server.log
```

If you open this file with something like notepad++/vscode/atom then do a 'find' for the following string:-

```
Database corruption
```

If you see a match then proceed to the next FAQ, if not then please post on the support thread and attach the 'Plex Media Server.log' file, detailing what the issue is.

**UPDATE** - Limetech has identified the SQLite corruption issue and fixed it, this has been included in the next stable release (6.8.0), i would encourage anybody with the corruption issue to move to this when available, else switch back to 6.6.7.

**Q4.** OK i see i have Plex Database Corruption, how can i stop this happening?.

**A4.** In order to stop Database Corruption from happening again, you need to do **ONE** (or more) of the following:-

1. Switch to unRAID 6.6.7 - The SQLite bug is not present in this version, this is probably the simplest way around this issue until Limetech fix it in future releases ~~(still an issue with 6.8.0 RC3)~~

2. Move Plex metadata to cache drive - The bug is triggered by running unraid 6.7.x (or later) and storing the Plex metadata on the array, moving it to the cache drive will prevent the bug.

3. Use the 'Unassigned Devices' plugin to assign a separate drive - Similar to moving to a cache drive, this also prevents the bug by moving the Plex metadata off the array and onto a single drive.

**Q5.** So I've now prevented the Database Corruption bug from happening, how do i now fix the existing Plex Database?.

**A5.** The cleanest and safest way of doing this is to restore from a backup, if you run the plugin 'CA Appdata Backup/Restore v2' then you can simply restore your metadata and you should be good to go. 

If you don't have a backup then you can either attempt a repair of the corrupt database using the following instructions:- 
https://support.plex.tv/articles/201100678-repair-a-corrupt-database/

or simply delete everything in /config for this container and start from a clean state (ensuring at the end of the process you backup!).

**Q6.** Plex has suddenly stopped working and displays the message below in ```/config/supervisord.log```, what is the best way to diagnose what the issue is?

```
INFO gave up: plexmediaserver entered FATAL state, too many start retries too quickly
```

**A6.** There are multiple reasons this could happen, the best way to diagnose the issue is to look at the Plex Media Server log, this file is located here:-

```
/config/Plex Media Server/Logs/Plex Media Server.log
```

Open the log file with something like Notepad++/Atom/VSCode and search the log for the keywords 'error' or 'corruption' or 'fatal', if the issue is corruption (common issue) then see Q4 and Q5 above.