# **rTorrentVPN Docker FAQ**

**Q1.** Why are some of my settings reverting when I restart ruTorrent?

**A1.** ruTorrent is purely a web frontend to rTorrent, and as such does NOT modify any settings for rTorrent. The only settings you can save using ruTorrent are settings for ruTorrent itself, i.e., things like enabling/disabling plugins, settings for plugins, etc.

If you want to modify things like incoming port, enabling/disabling DHT, and folders for incomplete/complete downloads, then you will have to modify the rTorrent config file. This is located in `/config/rtorrent/config/rtorrent.rc`. Please make sure you use something like Notepad++ (not Windows Notepad) to prevent the line endings from being modified.

**Q2.** I'm seeing the following error in /config/supervisord.log. What does it mean, and how can I fix the issue?
```
[emerg] xxxxxx: open() "/etc/nginx/nginx.conf" failed (13: Permission denied)
```

**A2.** Synology appliances make use of something called 'AppArmor'. This is a Linux kernel security model that restricts program capabilities. Unfortunately, AppArmor is a little too overly restrictive and causes issues with Nginx, thus the 'Permission denied' error message. At present, the only way to fix this is to turn off AppArmor on the Synology device.

**Q3.** I have just updated and now I cannot authenticate using the default credentials for the web UI or access rTorrent via 3rd party applications such as Sonarr, Radarr, etc. What has changed, and how do I fix it?

**A3.** Historically, the rTorrentVPN Docker image has used hard-coded credentials (admin/rutorrent) for the web UI and rpc2 (used to allow applications to connect to rTorrent). While this means an easy way of logging in for the user, it also means potential exposure of this Docker container with weak credentials if left unchanged.

The decision has therefore been made to improve out-of-the-box security by setting a randomized password for the web UI and/or rpc2 if none are defined. This can be identified by one or both of the following messages in the '/config/supervisord.log' file:

```
[warn] RPC2_PASS not defined (via -e RPC2_PASS), using randomized password (password stored in '/config/nginx/security/rpc2_pass')
[warn] WEBUI_PASS not defined (via -e WEBUI_PASS), using randomized password (password stored in '/config/nginx/security/webui_pass')
```

If you see the above in your log, then you will need to open the files specified in order to retrieve your newly defined passwords. You would then use these passwords to log in to the web UI and/or reconfigure 3rd party apps to use the new rpc2 credentials.

If you want to specify your own passwords, then please read Q4 below.

**Q4.** OK, I have identified (via Q3) I need to define my credentials for the web UI and/or rpc2. How do I do this so that I don't have to use the randomly generated passwords?

**A4.** In order to set your own passwords for the web UI and/or rpc2, you need to create additional environment variables (if not already present).

The 'key' names will be:

```
WEBUI_PASS
```

and

```
RPC2_PASS
```

The 'value' for each of the above will be the password you want to set.

**NOTE** Unraid users: Creating environment variables is done by left-clicking the icon shown in the Docker tab in the Unraid web UI, select 'Edit', click on 'Add another Path, Port, Variable, Label or Device', then select 'Config Type' of 'Variable' and define the key and value as shown above.