# **unRAID Docker FAQ**

**Q1.** What does the Privileged check-box do?

**A1.** The Privileged checkbox allows the Docker Container to perform certain privileged activities, these are typically required for additional netwworking functions, such as creating/editing virtual adapters.

**Q2.** I can't see how to configure the settings for the VPN Docker images

**A2.** The current default action in the unRAID webui for Docker is to hide the Advanced options, for some applications you need to view these advanced options to configure the application using Environment Variables. To view these additional fields simply click on the "Advanced View" toggle button and then fill in the values.

**Q3.** I can see there is a newer version of the Docker application im running, can i update the application using the applications built-in update system?.

**A3.** In place upgrades are not recommended when using Docker applications, instead wait for the developer to trigger a new build. Once the new image has been built, then open the unRAID webui and click on the "Docker" tab and then press the "Check for Updates" button, this should then change the "Version" for the Docker container to "update ready" then simply click on this and click the "Just do it!" button to begin the download of the newer image.

**Q4.** Why can't Sonarr/Radarr/Lidarr/SickRage/Medusa post process my downloads from SABnzbd/Deluge/rTorrent?

**A4.** The location you set for downloads for your download client(s) MUST be consistent for ALL docker containers, so for instance assuming two containers (there could and probably will be more than two in reality), a downloader (sabnzbdvpn) and a metadata downloader (sonarr), here are some scenarios:-

**BROKEN EXAMPLE 1.**</span>

**sabnzbdvpn**

```/mnt/cache/appdata/data/completed```

is mapped to

```/data```

**sonarr**

```/mnt/cache/appdata/data```

 is mapped to

  ```/data```

Why is this broken? because although the container path (/data) is the same for both containers, the host path does NOT match

**BROKEN EXAMPLE 2.**</span>

**sabnzbdvpn**

```/mnt/cache/appdata/data/Completed```

is mapped to

```/data```

**sonarr**

```/mnt/cache/appdata/data/completed```

is mapped to

```/data```

Why is this broken? because although the container path (/data) is the same for both containers, the host path does NOT match (linux is CaSe sensitive).

**BROKEN EXAMPLE 3.**</span>

**sabnzbdvpn**

```/mnt/cache/appdata/data/completed```

is mapped to

```/data```

**sonarr**

```/mnt/cache/appdata/data/completed```

is mapped to

```/downloads```

Why is this broken? because although the host path is now ok, the container paths do NOT match.

**WORKING EXAMPLE**</span>

**sabnzbdvpn**

```/mnt/cache/appdata/data/completed``` 

is mapped to:-

```/data```

**sonarr**

```/mnt/cache/appdata/data/completed```

is mapped to:-

```/data```

Why is this working? because BOTH the container path (/data) and the host path (/mnt/cache/appdata/data/completed) EXACTLY match.

**IMPORTANT** - Application configuration

Lastly keep in mind that when you configure sabnzbdvpn and sonarr (in this example) the paths again must match, so if you configure sabnzbdvpn to download to /data/usenet/ then you MUST also configure sonarr to use the same path, you CANNOT configure sonarr to use /data, this again would cause a mismatch of path, even if you have set the container path and the host path to be the same (as in the working example above).

**Q5.** There is an issue with the latest version of an application, how do i roll back to a specific version?

**A5.** In order to pull down a specific version of an application you need to specify the tag with the version you want. To find out what tags are available for the docker image you need to go to the first post in the applications support thread, then copy the URL shown for "Docker Hub:" and append "builds/" to the end of the url and paste into your preferred browser.

This will return a list of available tag names, make a note of the tag you want (tag name denotes the version of the application) and then go the unRAID web interface, left clicking the specific Docker container and selecting "edit", then click on the advanced view option (top right) and edit the repository string, adding in ":<the tag you want>" to the end of the name, e.g. to specify a version of 1.0.0.0 for couchpotato. the repository would be changed from:-

```binhex/arch-couchpotato```

to

```binhex/arch-couchpotato:1.0.0.0```

**Q6.** I know Needo's Docker images automatically update on restart, can any of your Docker Images do this also?

**A6.** No, i have not gone down the route of having automatic upgrading of applications on reboot/start, two main reasons are around keeping the dockerfile code as clean and easy to maintain as possible, and secondly to do with the ease of support, if i know everybody is running the same version then its a LOT easier to offer support. I do keep a very close eye on versions of applications coming out and constantly monitor for changes, so the gap between release and the latest version being available for download via Docker webui is minimal (typically a couple of days).

**Q7.** I am seeing corruption with the applications database or issues when attempting to run Lidarr/Radarr/Sonarr/Plex/PlexPass, what could be the cause?

**A7.** Certain applications are not compatible with FUSE, which is part of unRAID, FUSE is used to join or fuse (thus the name) multiple disks together to create a logical view of your media. 
The FUSE system is seen when you browse any share with /mnt/user/ in the path, such as /mnt/user/appdata. Due to this limitation certain apps need to have their configuration files defined on a non FUSE share, to do this follow the procedure below:-

1. Stop the docker container that has the issues
2. Left click the icon in the unRAID Web UI and select 'edit'
3. Click on the toggle to switch to 'Advanced View' (top right)
4. Scroll down to the path defined for /config and change it:-

from

```/mnt/user/appdata/<container name>```

to (if you have a cache drive):-

```/mnt/cache/appdata/<container name>```

or (if you do NOT have a cache drive):-

```/mnt/disk<number>/appdata/<container name>```

5. Click on 'apply' to save the change
6. Check the application is running.

**Note**:- If you do NOT have a cache drive and you switch to a specific disk then you may need to reconfigure the application from scratch.
