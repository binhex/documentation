# **General Docker FAQ**

**Q1.** What is the difference between Bridge and Host network types?

**A1.** Setting a Docker container to Bridge networking (default) allows the user to map a port from the host to the container (see later questions), whereas setting a Docker container to use Host networking means the Docker application's ports cannot be defined and are bound to the host's adapter.

**Q2.** What are Volume Mappings used for?

**A2.** Volume Mappings are a way of sharing data from the host to the running Docker container. Without a Volume Mapping, it would be very difficult to access data written to the running container's virtual file system. Volume mappings are defined in two halves: the container volume, which is the root folder that will appear INSIDE the Docker container, and the host path, which is the full path you wish to share with the Docker container.

For example, a volume mapping of /config /mnt/cache/appdata will create a folder called "config" off the root of the Docker container's file system. This folder will contain all files and folders that exist in the host's path "/mnt/cache/appdata".

**IMPORTANT** - When configuring the Docker application (not container), remember to use the container volume root folder, NOT the host path, e.g., /data/completed for completed folder or /data/incomplete for incomplete downloads, NOT host path such as /mnt/user/appdata/completed.

**Q3.** What is the /config container volume used for?

**A3.** This is used to store application configuration, such as ini files, DBs, cached data, etc.

**Q4.** What is the /data container volume used for?

**A4.** This is used to store downloaded data generated from the Docker application, such as TV shows, movies, games, etc.

**Q5.** What is the /media container volume used for?

**A5.** This is used with Docker applications that index data for user consumption, or Docker applications that require access to your media library to perform post-processing. Example applications are CouchPotato, Plex, Madsonic, Sickbeard, SickRage.

**Q6.** What are Port Mappings used for?

**A6.** Port mappings are used to map a host's ports to a container's ports. This gives you the flexibility to have multiple containers running using the same port but defined as different ports on the host side.

**IMPORTANT** - When editing the Docker container, please do NOT alter the container port. This is set in the Docker image and should not be changed. This also applies to application configuration; the port number should NOT be changed.

**Q7.** Why is there more than one container port specified?

**A7.** Multiple container ports are sometimes required for applications where there is more than one process running. An example of this would be Deluge, where it has a daemon (process that does the downloading), a web UI (process serving the HTTP interface), and an additional port for incoming requests.

**Q8.** I have a problem with a Docker, are there any logs?

**A8.** All the Docker containers for this repository use a process manager called Supervisor. This will log stdout and stderr to a log file called "supervisord.log" in the root of the defined /config host path. When logging an issue on the forum, please attach this to help diagnose the issue quicker.

**Q9.** I have a problem with application X, it's hanging/crashing/behaving strangely. Who do I contact?

**A9.** Although I am the developer for the Docker image, I cannot fix issues related to the application itself. Put simply, I create an easy-to-use method to run the application. If the application itself is faulty, then you will need to contact the developer(s) of the application. Most of the time this involves posting an 'issue' on GitHub. See the application support thread OP for links to the application, or link in the readme.md for the application.

**Q10.** Since the latest update I am having issues with application MineOS/Minecraft/LibreOffice/PyCharm and it is unable to start. What is the cause of this and how can I fix it?

**A10.** Due to an update to the 'glibc' library used in the Arch Linux base OS that all my images are built on, it is necessary to upgrade the version of 'runc' to '1.0-rc93' or later. To do this, perform **ONE** of the following actions:

1. Update the version of Docker (**non-Unraid users**) - This is by far the simplest way of upgrading runc, as the latest version is included in the latest Docker release. Please refer to your distro documentation on how to update to the latest version of Docker.

2. Update runc manually (**Unraid users**) - The steps are as follows:

Drop to Terminal for the Unraid server (NOT the container) and issue the following command to upgrade runc:

```bash
curl -o '/usr/bin/runc' -L 'https://github.com/binhex/arch-packages/raw/master/static/x86-64/runc/runc' && chmod +x '/usr/bin/runc'
```

**Note**: The above step will be necessary on subsequent restarts of the server (NOT the container).

This is a temporary stopgap whilst we wait for the next release of Unraid (6.9.2), which should include the latest version of Docker (I have contacted Limetech).

3. Switch to privileged mode - This is a workaround if you do not want to update runc, but it does elevate permissions for the container and thus increases the potential for a security issue. Unraid users can do this by going to web UI > Docker tab > left-click icon and select 'Edit', then toggle the `Privileged` to `on` and click on Apply. For non-Unraid users, they simply supply the additional flag when creating the container `--privileged=true`.

4. Roll back to previous version - If none of the above options are suitable, then the only option left is to roll back to a previous version before the glibc update by using a specific tagged image. See Q5 from the following link: [https://github.com/binhex/documentation/blob/master/docker/faq/unraid.md](https://github.com/binhex/documentation/blob/master/docker/faq/unraid.md)

**Q11.** I want to use a particular version of an application. How can I do this using Docker containers?

**A11.** I have implemented sub-version tagging for all images going forward (no retro version tags). This means if you are sensitive to version changes for any application, you can now restrict this by specifying the version you want in a more exact manner. The tags generated are as follows:

```text
<major version>
<major version>.<minor version>
<full version>
'latest'
```

So, for example, a version of 'v1.45.0' would generate the following tags:

```text
v1
v1.45
v1.45.0
latest
```

So if you wanted to only use v1, then you could specify `<repo name>:v1`, or if you wanted to use the current major-minor version then `<repo name>:v1.45`.

**Note**: Do keep in mind sub-version tags will only be generated for applications that comply with semver, that is, 3 (or more) octets in the version. Not all application developers do this.

A list of available Docker tags can be seen at: `https://hub.docker.com/r/binhex/arch-<app name>/tags`, for example for the binhex Sonarr Docker image tags will be seen at: [https://hub.docker.com/r/binhex/arch-sonarr/tags](https://hub.docker.com/r/binhex/arch-sonarr/tags)
