# **Plex Docker FAQ**

**Q1.** How do I configure Plex to transcode to RAM/Array/Cache?

**A1.** Below are the different options for setting transcoding for Plex and Plex Pass:-

**Transcode to RAM**

1. Go to unRAID web ui/Docker tab/left click Plex container and select 'edit'
2. Click on the toggle for 'advanced view'
3. Go to variable named 'TRANS_DIR' and set the value to ```/transcode```
4. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Path'
5. Set 'Container Path' to ```/transcode``` and 'Host Path' to ```/tmp```
6. Click on 'Add'
7. Click on 'Apply' to apply the change

**Transcode to the array** 

1. Go to unRAID web ui/Docker tab/left click Plex container and select 'edit'
2. Click on the toggle for 'advanced view'
3. Go to variable named 'TRANS_DIR' and set the value to ```/transcode```
4. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Path'
5. Set 'Container Path' to ```/transcode``` and 'Host Path' to ```/mnt/user/<share name to store transcodes>```
6. Click on 'Add'
7. Click on 'Apply' to apply the change

**Transcode to cache** (recommended)

1. Go to unRAID web ui/Docker tab/left click Plex container and select 'edit'
2. Click on the toggle for 'advanced view'
3. Go to variable named 'TRANS_DIR' and set the value to ```/transcode```
4. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Path'
5. Set 'Container Path' to ```/transcode``` and 'Host Path' to ```/mnt/cache/appdata/binhex-plex/tmp```
6. Click on 'Add'
7. Click on 'Apply' to apply the change

**Q2.** I would like to control the size of the RAM used when transcoding to RAM, is this possible and if so how do i do this?.

**A2.** This can be achieved by creating a RAM Disk and using this as the area to transcode to, the procedure is as follows:-

1. Go to the unRAID 'Terminal' (NOT container console) and issue the following command to create a 5GB RAM Disk:-
```mkdir -p /tmp/plex-ramdisk && mount -t tmpfs -o size=5g tmpfs /tmp/plex-ramdisk```
2. Follow the procedure for **A1.** 'Transcode to RAM', substituting 'Host Path' ```/tmp``` for ```/tmp/plex-ramdisk```

**Notes**  
- If you want the RAM Disk to be larger then change the ```size=``` parameter for the command.
- Location of the RAM Disk can be anywhere and does not have to be located under '/tmp'.
- The command shown above will not be persistent and therefor will need to be re-run on subsequent reboots, a way of automating this is to include the command in the unRAID boot script named 'go', you can do this by running the command below from the unRAID 'Terminal':-
```echo 'mkdir -p /tmp/plex-ramdisk && mount -t tmpfs -o size=5g tmpfs /tmp/plex-ramdisk' >> '/boot/config/go'```

**Q3.** How do I configure Plex to use my GPU for encoding/decoding (sometimes referred to as hardware transcoding)?

**A3.** To enable GPU encoding/decoding within Plex you need to install the 'Nvidia Driver' Plugin and configure two variables:-

1. Go to Community Applications in the unRAID web ui and search for Plugin 'Nvidia Driver' and install it, then reboot host.
2. Go to unRAID web ui/Plugins/Nvidia Driver and make a note of the GPU device, it should look something like this:- ```GPU-02ff3633-4f22-c4d6-2c15-654ff33a321e```
2. Go to unRAID web ui/Docker tab/left click Plex container and select 'edit'.
3. Click on the toggle for 'advanced view'.
4. Go to variable named 'NVIDIA_DRIVER_CAPABILITIES' and set the value to ```all```
5. Go to variable named 'NVIDIA_VISIBLE_DEVICES' and set the value to the GPU device found in step 2.
6. Start Plex container.

**Notes**  
It is possible that the variables mentioned above do not exist in your template, if this is the case then please create them by doing the following:-

1. Go to unRAID web ui/Docker tab/left click Plex container and select 'edit'
2. Click on the toggle for 'advanced view'
3. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Variable'
4. Set 'Key' to the name of the variable mentioned above, and the 'Value' to the value mentioned above, repeat for both variables.

**Q4.** Plex has suddenly stopped working and displays the message below in ```/config/supervisord.log```, what is the best way to diagnose what the issue is?
```
INFO gave up: plexmediaserver entered FATAL state, too many start retries too quickly
```

**A4.** There are multiple reasons this could happen, the best way to diagnose the issue is to look at the Plex Media Server log, this file is located here:-
```
/config/Plex Media Server/Logs/Plex Media Server.log
```
Open the log file with something like Notepad++/Atom/VSCode and search the log for the keywords 'error' or 'corruption' or 'fatal', if the issue is corruption (common issue) then see Q5 below.

**Q5.** So i see from Q4 that i do have Plex daatabase corruption, how can i attempt to fix this?.

**A5.** The cleanest and safest way of doing this is to restore from a backup, if you run the plugin 'CA Appdata Backup/Restore v2' then you can simply restore your metadata and this should get you up and running.

If you don't have a backup then you can attempt a repair of the corrupt database using the following instructions:- 
https://support.plex.tv/articles/201100678-repair-a-corrupt-database/

If the repair database procedure does not work then you can attempt a roll back to a previous database backup by doing the following:-

1. Stop the Plex container.
2. Rename the current database file ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db``` to ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db.orig```
3. Rename the current database file ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db``` to ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db.orig```
4. Rename the newest backup database file (example) ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db-2021-04-03``` to ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db```
5. Rename the newest backup database file (example) ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db-2021-04-03``` to ```/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db```
6. Start the Plex container and scrape any missing metadata.

**Notes**  
The above procedure will cause some loss of metadata, as you will be rolling back to a point in time (typically 3 days prior) but it maybe necessary to go back further if the database corruption happens some time ago, so further metadata scraping maybe required for your library after the restore.
