# **Plex Docker FAQ**

**Q1.** How do I configure Plex to transcode to RAM/Array/Cache?

**A1.** Below are the different options for setting transcoding for Plex and Plex Pass:

**Transcode to RAM**

1. Go to Unraid web UI > Docker tab > left-click Plex container and select 'Edit'.
2. Click on the toggle for 'Advanced View'.
3. Go to the variable named 'TRANS_DIR' and set the value to `/transcode`.
4. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Path'.
5. Set 'Container Path' to `/transcode` and 'Host Path' to `/tmp`.
6. Click on 'Add'.
7. Click on 'Apply' to apply the change.

**Transcode to the array**

1. Go to Unraid web UI > Docker tab > left-click Plex container and select 'Edit'.
2. Click on the toggle for 'Advanced View'.
3. Go to the variable named 'TRANS_DIR' and set the value to `/transcode`.
4. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Path'.
5. Set 'Container Path' to `/transcode` and 'Host Path' to `/mnt/user/<share name to store transcodes>`.
6. Click on 'Add'.
7. Click on 'Apply' to apply the change.

**Transcode to cache** (recommended)

1. Go to Unraid web UI > Docker tab > left-click Plex container and select 'Edit'.
2. Click on the toggle for 'Advanced View'.
3. Go to the variable named 'TRANS_DIR' and set the value to `/transcode`.
4. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Path'.
5. Set 'Container Path' to `/transcode` and 'Host Path' to `/mnt/cache/appdata/binhex-plex/tmp`.
6. Click on 'Add'.
7. Click on 'Apply' to apply the change.

**Q2.** I would like to control the size of the RAM used when transcoding to RAM. Is this possible, and if so, how do I do this?

**A2.** This can be achieved by creating a RAM disk and using this as the area to transcode to. The procedure is as follows:

1. Go to the Unraid 'Terminal' (NOT container console) and issue the following command to create a 5GB RAM disk:
```mkdir -p /tmp/plex-ramdisk && mount -t tmpfs -o size=5g tmpfs /tmp/plex-ramdisk```
2. Follow the procedure for **A1.** 'Transcode to RAM', substituting 'Host Path' `/tmp` for `/tmp/plex-ramdisk`.

**Notes**<br/>
- If you want the RAM disk to be larger, then change the `size=` parameter for the command.
- The location of the RAM disk can be anywhere and does not have to be located under '/tmp'.
- The command shown above will not be persistent and therefore will need to be re-run on subsequent reboots. A way of automating this is to include the command in the Unraid boot script named 'go'. You can do this by running the command below from the Unraid 'Terminal':
```echo 'mkdir -p /tmp/plex-ramdisk && mount -t tmpfs -o size=5g tmpfs /tmp/plex-ramdisk' >> '/boot/config/go'```

**Q3.** How do I configure Plex to use my GPU for encoding/decoding (sometimes referred to as hardware transcoding)?

**A3.** To enable GPU encoding/decoding within Plex, you need to install the 'Nvidia Driver' plugin and then configure the Plex container as follows:

1. Go to Community Applications in the Unraid web UI and search for the plugin 'Nvidia Driver' and install it, then reboot the host.
2. Go to Unraid web UI > Plugins > Nvidia Driver and make a note of the GPU device. It should look something like this: `GPU-02ff3633-4f22-c4d6-2c15-654ff33a321e`.
3. Go to Unraid web UI > Docker tab > left-click Plex container and select 'Edit'.
4. Click on the toggle for 'Advanced View'.
5. Go to 'Extra Parameters:' and set it to `--runtime=nvidia`.
6. Go to the variable named 'NVIDIA_DRIVER_CAPABILITIES' and set the value to `all`.
7. Go to the variable named 'NVIDIA_VISIBLE_DEVICES' and set the value to the GPU device found in step 2.
8. Start the Plex container.

**Notes**<br/>
It is possible that the variables mentioned above do not exist in your template. If this is the case, then please create them by doing the following:

1. Go to Unraid web UI > Docker tab > left-click Plex container and select 'Edit'.
2. Click on the toggle for 'Advanced View'.
3. Click on 'Add another Path, Port, Variable, Label or Device' and select 'Config Type' of 'Variable'.
4. Set 'Key' to the name of the variable mentioned above, and the 'Value' to the value mentioned above. Repeat for both variables.

**Q4.** Plex has suddenly stopped working and displays the message below in `/config/supervisord.log`. What is the best way to diagnose what the issue is?
```
INFO gave up: plexmediaserver entered FATAL state, too many start retries too quickly
```

**A4.** There are multiple reasons this could happen. The best way to diagnose the issue is to look at the Plex Media Server log. This file is located here:
```
/config/Plex Media Server/Logs/Plex Media Server.log
```
Open the log file with something like Notepad++, Atom, or VSCode and search the log for the keywords 'error', 'corruption', or 'fatal'. If the issue is corruption (a common issue), then see Q5 below.

**Q5.** So I see from Q4 that I do have Plex database corruption. How can I attempt to fix this?

**A5.** There are three methods for recovering from database corruption. These are listed below in preferred order:

1. Restore from backup using the Unraid plugin 'APPDATA.BACKUP' (recommended) - See support thread [here](https://forums.unraid.net/topic/137710-plugin-appdatabackup/)
2. Attempt rollback to built-in database backup - See 'Rollback database' below.
3. Attempt database repair using the built-in script - See 'Repair database' below.

**Rollback database**
1. Stop the Plex container.
2. Rename the current database file `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db` to `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db.orig`.
3. Rename the current database file `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db` to `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db.orig`.
4. Rename the newest backup database file (example) `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db-2021-04-03` to `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.db`.
5. Rename the newest backup database file (example) `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db-2021-04-03` to `/config/Plex Media Server/Plug-in Support/Databases/com.plexapp.plugins.library.blobs.db`.
6. Start the Plex container and scrape any missing metadata.

**Notes**<br/>
The above procedures MAY cause loss of metadata (NOT media), as you will be rolling back to a point in time (typically 3 days prior), but it may be necessary to go back further if the database corruption happened some time ago, so further metadata scraping may be required for your library after the restore.

**Repair database**
1. Start the Plex container.
2. Left-click the icon in the Unraid web UI and select 'Console'.
3. Type `/home/nobody/dbrepair.sh`.
4. Select option `Check integrity`. Once this has completed, exit the console and restart the container. If this still has not fixed it, move on to the next step.
5. Select option `Repair structure (basic repair)`. Once this has completed, exit the console and restart the container. If this still has not fixed it, move on to the next step.
6. Select option `Rebuild indexes`. Once this has completed, exit the console and restart the container. If this still has not fixed it, move on to the next step.
7. Select option `Low-level recovery`. Once this has completed, exit the console and restart the container. If this still has not fixed it, move on to the next step.
8. If none of the steps above have fixed the database, then the only course of action is to reconfigure Plex from scratch.

**Notes**<br/>
The above procedures MAY cause loss of metadata (NOT media) and other unforeseen issues (configuration). Repairing the database will result in deletion of the corrupt data, so further metadata scraping and/or configuration may be required after the repair.
