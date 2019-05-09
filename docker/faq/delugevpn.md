# **DelugeVPN Docker FAQ**

**Q1.** How do i connect CouchPotato to DelugeVPN?

**A1.** To connect CouchPotato to DelugeVPN perform the following:-

**IMPORTANT** - If your connecting to DelugeVPN from a traditional (non dockerized) installation of CouchPotato then please ensure you have configured the LAN_NETWORK (see FAQ "I'm struggling to configure LAN_NETWORK correctly, can you give some examples?" for help) environment variable for DelugeVPN (you can left click and "Edit" the configuration if you've already created the container).

1. Start DelugeVPN and login to the webui, then enable Preferences->Daemon->Allow Remote Connections

2. Restart DelugeVPN Docker container

3. Open Deluge authorisation file /config/auth and note the username and password, it should be in the format:-

&nbsp;&nbsp; ```<admin username>:<admin password>:<level>```

&nbsp;&nbsp;&nbsp; The default value is as follows:-

&nbsp;&nbsp; ```admin:deluge:10```

&nbsp;&nbsp;&nbsp; If the above doesn't exist then please add to the auth file and restart the container before continuing.

4. Go to the CouchPotato webui->Settings>Downloaders>Deluge

5. Configure Host as ```<host ip address>:58846```

6. Configure the Username and Password as specified in the auth file

7. Click on Test Deluge button, if it all works then you should see success.

**Q2.** How can i verify that the Torrent client is using the VPN tunnel?

**A2.** You can verify this by loading a custom torrent that returns the IP address that is used to connect to the tracker, instructions on how to do this shown below:-

1. Go to website http://torguard.net/checkmytorrentipaddress.php

2. Click on the "Check my torrent IP" button to download the torrent

3. Open Deluge webui and add the torrent

4. Highlight the added torrent and go to the "Status" tab at the bottom left of the screen

5. The "Tracker Status" should return something like this:-

&nbsp;&nbsp; ```"checkmytorrentip.net: Error: Success, Your torrent client IP is: 10.156.1.1"```

6. If the tunnel is correctly established then IP address should be different to the IP address from your ISP.
