# **DelugeVPN Docker FAQ**

**Q1.** How do I connect CouchPotato to DelugeVPN?

**A1.** To connect CouchPotato to DelugeVPN, perform the following:

**IMPORTANT** - If you're connecting to DelugeVPN from a traditional (non-dockerized) installation of CouchPotato, then please ensure you have configured the LAN_NETWORK (see FAQ "I'm struggling to configure LAN_NETWORK correctly, can you give some examples?" for help) environment variable for DelugeVPN (you can left-click and "Edit" the configuration if you've already created the container).

1. Start DelugeVPN and log in to the web UI, then enable Preferences > Daemon > Allow Remote Connections.
2. Restart the DelugeVPN Docker container.
3. Open the Deluge authorization file `/config/auth` and note the username and password. It should be in the format:

   ```<admin username>:<admin password>:<level>```

   The default value is as follows:

   ```admin:deluge:10```

   If the above doesn't exist, then please add it to the auth file and restart the container before continuing.

4. Go to the CouchPotato web UI > Settings > Downloaders > Deluge.
5. Configure Host as `<host ip address>:58846`.
6. Configure the Username and Password as specified in the auth file.
7. Click on the Test Deluge button. If it all works, then you should see success.

**Q2.** How can I verify that the torrent client is using the VPN tunnel?

**A2.** You can verify this by loading a custom torrent that returns the IP address that is used to connect to the tracker. Instructions on how to do this are shown below:

1. Go to the website http://torguard.net/checkmytorrentipaddress.php
2. Click on the "Check my torrent IP" button to download the torrent.
3. Open the Deluge web UI and add the torrent.
4. Highlight the added torrent and go to the "Status" tab at the bottom left of the screen.
5. The "Tracker Status" should return something like this:

   ```"checkmytorrentip.net: Error: Success, Your torrent client IP is: 10.156.1.1"```

6. If the tunnel is correctly established, then the IP address should be different from the IP address from your ISP.

**Q3.** When I attempt to install a 3rd party Deluge plugin using the Web UI, it doesn't load. What is the correct way to install 3rd party plugins?

**A3.** Installing .egg files (Google 'Python Eggs' for more info) is best done by copying and pasting the file with the .egg extension to the `/config/plugins/` folder on the host. Once you have done this, you will need to restart the container for the plugin to be shown in Preferences > Plugins in the Web UI. If the plugin does not stay enabled between restarts, then see below.

**Q4.** Whenever I enable a plugin using the Deluge Web UI, the enabled plugin seems to disable on a restart. How can I keep the plugin enabled even after a restart?

**A4.** Deluge developers seem to have taken the strange decision of writing configuration changes for plugins on shutdown. This causes a problem for people using Deluge in a container, as all processes will be terminated on shutdown of the container, meaning there is no time for Deluge to write out to the configuration file (`/config/core.conf`). To get around this, we need to manually edit the configuration file ourselves. The steps below detail how to do this:

1. Make a note of the plugin names you want to enable through the Web UI (they are case sensitive!).
2. Stop the container and then open `/config/core.conf` with a text editor such as Notepad++ (Windows) or nano (Linux). Do NOT use WordPad or Notepad on Windows, as it will screw up line endings.
3. Find the section 'enabled_plugins' and add in the list of plugins you want enabled. For example, to enable 3 plugins:

```
    "enabled_plugins": [
        "Label",
        "Scheduler",
        "Notifications"
    ],
```

4. Start the container again, then check the Web UI Preferences > Plugins and ensure the plugins are enabled.
5. Configure each plugin as desired.