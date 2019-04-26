# **General Docker FAQ**

Q1. What is the difference between Bridge and Host Network Types?

A1. Setting a Docker Container to Bridge networking (default) allows the user to map a port from the host to the container (see later questions), whereas setting a Docker container to use Host networking means the Docker applications ports cannot be defined and are bound to the Host's adapter

Q2. What are Volume Mappings used for?

A2. Volume Mappings are a way of sharing data from the host to the running Docker Container, without a Volume Mapping it would be very difficult to access data written to the running containers virtual file system. Volume mappings are defined in two halves, the Container volume, which is the  root folder that will appear INSIDE the Docker Container, and the Host path, which is the full path you wish to share with the Docker Container.

For example a volume mapping of /config /mnt/cache/appdata will create a folder called "config" off the root of the Docker Containers file system, this folder will contain all files and folders that exist in the hosts path "/mnt/cache/appdata".

**IMPORTANT** - When configuring the Docker application (not container) remember to use the container volume root folder, NOT the Host path, e.g. /data/completed for completed folder or /data/incomplete for incomplete downloads, NOT host path such as /mnt/user/appdata/completed

Q3. What is the /config Container Volume used for?

A3. This is used to store application configuration, such as ini files, db's, cached data, etc.

Q4. What is the /data Container Volume used for?

A4. This is used to store downloaded data generated from the Docker application, such as TV Shows, Movies, Games, etc.

Q5. What is the /media Container Volume used for?

A5. This is used with Docker applications that index data for user consumption, or Docker applications that require access to your media library to perform post processing, example applications are CouchPotato, Plex, Madsonic, Sickbeard, SickRage.

Q6. What are Port Mappings used for?

A6. Port mappings are used to map a hosts ports to a containers ports, this gives you the flexibility to have multiple containers running using the same port but are defined as different ports on the host side.

**IMPORTANT** - When editing the Docker container please do NOT alter the container port, this is set in the Docker image and should not be changed, this also applies to application configuration, the port number should NOT be changed.

Q7. Why is there more than one Container Port specified?

A7. Multiple Container ports are sometimes required for applications where there is more than one process running, an example of this would be Deluge, where it has a daemon (process that does the downloading), a webui (process serving the http interface), and an additional port for incoming requests.

Q8. I have a problem with a Docker, are there any logs?

A8. All the Docker containers for this repository use a process manager called Supervisor, this will log stdout and stderr to a log file called "supervisord.log" in the root of the defined /config hosts path. When logging an issue on the forum please attach this to help diagnose the issue quicker.

Q9. I have a problem with application X, its hanging/crashing/behaving strangely, who do i contact?

A9. Although i am the developer for the docker image i cannot fix issues related to the application itself, put simply i create an easy to use method to run the application, if the application itself is faulty then you will need to contact the developer(s) of the application, most of the time this involves posting an 'issue' on github, see the application support thread OP for links to the application, or link in the readme.md for the application.

Still stuck?, take a look at the unRAID FAQ:- https://lime-technology.com/forums/topic/57181-real-docker-faq/
