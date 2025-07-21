# Unraid Docker FAQ

**Q1.** What does the Privileged checkbox do?

**A1.** The Privileged checkbox allows the Docker container to perform certain privileged activities. These are typically required for additional networking functions, such as creating/editing virtual adapters

**Q2.** I can't see how to configure the settings for the VPN Docker images

**A2.** The current default action in the Unraid web UI for Docker is to hide the Advanced options. For some applications, you need to view these advanced options to configure the application using environment variables. To view these additional fields, simply click on the 'Advanced View' toggle button and then fill in the values

**Q3.** I can see there is a newer version of the Docker application I'm running. Can I update the application using the application's built-in update system?

**A3.** In-place upgrades are not supported and could possibly break the Docker container. Instead, to check and apply any pending updates, open the Unraid web UI and click on the 'Docker' tab and then press the 'Check for Updates' button (bottom of the screen). This should then change the 'Version' for the Docker container to 'update ready'. Then simply click on 'Apply Update' and click the 'Yes Update it!' button to begin the download of the newer image. If you wish to update all Docker images at the same time, then press the 'Check for Updates' button, wait for it to finish, and then click the 'Update All' button (bottom of the screen)

**Q4.** Why can't my metadata application (e.g., Sonarr/Radarr/Lidarr/SickRage/Medusa) post-process my downloads from download client (e.g., NZBGet/SABnzbd/Deluge/qBittorrent/rTorrent)? It seems to be reporting that the file just downloaded cannot be found. What is going on here and how can I fix it?

**A4.** The location you set for downloads MUST be consistent for the metadata container(s) and the download container(s). So for instance, assuming two containers (there could and probably will be more than two in reality), a downloader (sabnzbdvpn) and a metadata downloader (sonarr), here are some scenarios

**Broken Example 1.- SABnzbdVPN

Host path is set to:
```/mnt/cache/appdata/data/completed```

Container path is set to:
```/data```

**Broken Example 1.- Sonarr

Host path is set to:
```/mnt/cache/appdata/data```

Container path is set to:
  ```/data```

Why is this broken? Because although the container path (/data) is the same for both containers, the host path does NOT match.

**Broken Example 2.- SABnzbdVPN

Host path is set to:
```/mnt/cache/appdata/data/Completed```

Container path is set to:
```/data```

**Broken Example 2.- Sonarr

Host path is set to:
```/mnt/cache/appdata/data/completed```

Container path is set to:
```/data```

Why is this broken? Because although the container path (/data) is the same for both containers, the host path does NOT match (Linux is case sensitive).

**Broken Example 3.- SABnzbdVPN

Host path is set to:
```/mnt/cache/appdata/data/completed```

Container path is set to:
```/data```

**Broken Example 3.- Sonarr

Host path is set to:
```/mnt/cache/appdata/data/completed```

Container path is set to:
```/downloads```

Why is this broken? Because although the host path is now ok, the container paths do NOT match.

**Working Example 1.- SABnzbdVPN

Host path is set to:
```/mnt/cache/appdata/data/completed```

Container path is set to:
```/data```

**Working Example 1.- Sonarr

Host path is set to:
```/mnt/cache/appdata/data/completed```

Container path is set to:
```/data```

Why is this working? Because BOTH the container path (/data) and the host path (/mnt/cache/appdata/data/completed) EXACTLY match.

**IMPORTANT** - Application configuration

Lastly, keep in mind that when you configure sabnzbdvpn and sonarr (in this example), the paths again must match. So if you configure sabnzbdvpn to download to /data/usenet/ then you MUST also configure sonarr to use the same path. You CANNOT configure sonarr to use /data; this again would cause a mismatch of path, even if you have set the container path and the host path to be the same (as in the working example above).

**Q5.** There is an issue with the latest version of an application. How do I roll back to a specific version?

**A5.** In order to pull down a specific version of an application, you need to specify the tag with the version you want. To find out what tags are available for the Docker image, you need to go to the first post in the application's support thread, then copy the URL shown after the text 'Docker Hub:' and append 'tags/' to the end of the URL and paste into your preferred browser

This will return a list of available tag names. Make a note of the tag you want (tag name denotes the version of the application) and then go to the Unraid web interface, left-clicking the specific Docker container and selecting 'Edit', then click on the advanced view option (top right) and edit the repository string, adding in `:<the tag you want>` to the end of the name. E.g., to specify a version of 1.0.0.0 for CouchPotato, the repository would be changed from:

```binhex/arch-couchpotato```

to

```binhex/arch-couchpotato:1.0.0.0```

**Q6.** I know Needo's Docker images automatically update on restart. Can any of your Docker images do this also?

**A6.** No, I have not gone down the route of having automatic upgrading of applications on reboot/start. Two main reasons are around keeping the Dockerfile code as clean and easy to maintain as possible, and secondly to do with the ease of support. If I know everybody is running the same version, then it's a LOT easier to offer support. I do keep a very close eye on versions of applications coming out and constantly monitor for changes, so the gap between release and the latest version being available for download via Docker web UI is minimal (typically a couple of days)

**Q7.** I am seeing corruption with the application's database or issues when attempting to run Lidarr/Radarr/Sonarr/Plex/PlexPass. What could be the cause?

**A7.** Certain applications are not compatible with FUSE, which is part of Unraid. FUSE is used to join or fuse (thus the name) multiple disks together to create a logical view of your media. The FUSE system is seen when you browse any share with /mnt/user/ in the path, such as /mnt/user/appdata. Due to this limitation, certain apps need to have their configuration files defined on a non-FUSE share. To do this, follow the procedure below

1. Stop the Docker container that has the issues
2. Left-click the icon in the Unraid web UI and select 'Edit'.
3. Click on the toggle to switch to 'Advanced View' (top right).
4. Scroll down to the path defined for /config and change it:

from

```/mnt/user/appdata/<container name>```

to (if you have a cache drive):

```/mnt/cache/appdata/<container name>```

or (if you do NOT have a cache drive):

```/mnt/disk<number>/appdata/<container name>```

5. Click on 'Apply' to save the change.
6. Check the application is running.

**Note**: If you do NOT have a cache drive and you switch to a specific disk, then you may need to reconfigure the application from scratch.

**Q8.** I have seen on the support thread on the forum that an additional feature has been added that requires me to add in values for a named variable XXXXX (e.g., VPN_INPUT_PORTS), but when I go to 'Edit' for the container and look for this I cannot see it. How can I fix this?

**A8.** Unraid does not automatically push out new 'Variables' when they are added to the template by the Docker image developer. You will instead need to add any additional 'Variables' in yourself. The procedure for doing this is as follows

1. Left-click on the container you want to add in the new feature and select `Edit`.
2. Click on the `Advanced view` toggle at the top right of the screen.
3. Click on the link `Add another Path, Port, Variable, Label or Device` at the bottom.
4. On the `Config Type` dropdown, select `Variable`.
5. Set the `Key:` to the name of the feature to add, e.g., `VPN_INPUT_PORTS` (ask on the support thread on the forum if you are unsure of the name).
6. Set the `Value:` to be the value for the variable. For the above example, that would be something like `1234` (ask on the support thread on the forum if you are unsure of the value).
7. Click on `ADD` to add the variable and then click on `Apply` to apply the change.

The alternative to the above is to delete the existing template and re-download from Community Applications (CA) and then reconfigure from scratch, but this is obviously not the ideal approach and should only be done as a last resort.

**Note**: Some variables allow you to define more than one value. This is generally done by using a comma to separate the `Value:`. In the example above, that would be something like `1234,5678`—if you are unsure whether the variable supports multiple values, then please ask on the support thread on the forum.

Still stuck? Take a look at the [Unraid FAQ](https://forums.unraid.nets/topic/57181-real-docker-faq/).

**Q9.** I see that the latest templates for Nicotine+/qBittorrent/Deluge have an env var called `GLUETUN_INCOMING_PORT`, what does this do?

**A9.** This env var if set to a value of `yes` will automatically assign the port forward from gluetun to Nicotine+/qBittorrent/Deluge, it will also bind the listening adapter to the VPN adapter for additional security against IP leakage. In order for this to work you must be sharing the network of an existing gluetun container, and the gluetun container must have port forwarding via the env var `VPN_PORT_FORWARDING` set to `on`

**Note** PIA users should note that wireguard support is NOT currently implemented in gluetun, so you will have to use OpenVPN.

**Q10.** I see that the latest template has an env var for healthchecking, namely `HEALTHCHECK_COMMAND`, what does this do?

**A10.** The env var `HEALTHCHECK_COMMAND` allows a user to define what command they want to execute to determine if the container is healthy or not. If no value is given then the default healthcheck will be used, this is a test of internet connectivity and a test to ensure the expected process is running

**Q11.** I see that the latest template has an env var for healthchecking, namely `HEALTHCHECK_ACTION`, what does this do?

**A11.** The env var `HEALTHCHECK_ACTION` allows a user to define wht aciton is performed if the containr becomes unhealthy (defined via `HEALTHCHECK_COMMAND` - see Q10 ), if no value is given then the default action is to return an non zero exit code, this will mark the container as 'unhealthly` but will take no further action

**Q12.** I want to automaticallt restart the container if `HEALTHCHECK_COMMAND` fails, how can I do this?

**A12.** In order to autatically restart the container on a healthcheck failure you would need to set two things, firstly set the `HEALTHCHECK_ACTION` to `kill 1` (will send SIGTERM to all processes), and secondly append to `Extra Parameters:` (toggle 'ADVANCED VIEW' to see this)  the flag `--restart=always`
