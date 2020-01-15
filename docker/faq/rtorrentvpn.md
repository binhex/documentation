# **rTorrentVPN Docker FAQ**

**Q1.** Why are some of my settings reverting when i restart rutorrent?

**A1.** So rutorrent is purely a web frontend to rtorrent, and as such does NOT modify any settings for rtorrent, the only settings you can save using rutorrent are settings for rutorrent itself, i.e. things like enabling/disabling plugins, settings for plugins etc.

If you want to modify things like incoming port, enabling/disabling dht, and folders for incomplete/complete downloads then you will have to modify the rtorrent config file, this is located in ```/config/rtorrent/config/rtorrent.rc``` please make sure you use something like notepad++ (not windows notepad) to prevent the line endings being modified.

**Q2.** I'm seeing the following error in /config/supervisord.log, what does it mean and how can i fix the issue?
```
[emerg] xxxxxx: open() "/etc/nginx/nginx.conf" failed (13: Permission denied)
```

**A2.** Synology appliances make use of something called 'AppArmor', this is a Linux kernel security model that restricts program capabilities. Unfortunately AppArmor is a little too overly restrictive and causes issues with NginX, thus the 'Permission denied' error message, at present the only way to fix this is to turn off AppArmor on the Synology device.

**Q3.** I have just updated and now i cannot authenticate using the default credentials for the web ui or access rtorrent via 3rd party applications such as Sonarr, Radarr, etc, what has changed and how do i fix it?

**A3.** Historically the rtorrentvpn docker image has used hard coded credentials (admin/rutorrent) for the web ui and rpc2 (used to allow applications to connect to rtorrent), and whilst this means a easy way of logging in for the user it does also mean potential exposure of this docker container with weak credentials if left unchanged. 

The decision has therefore been made to improve out of the box security by setting a randomised password for the web ui and/or rpc2 if none are defined, this can be identified by one or both of the following messages in the '/config/supervisord.log' file:-

```
[warn] RPC2_PASS not defined (via -e RPC2_PASS), using randomised password (password stored in '/config/nginx/security/rpc2_pass')
[warn] WEBUI_PASS not defined (via -e WEBUI_PASS), using randomised password (password stored in '/config/nginx/security/webui_pass')
```

If you see the above in your log then you will need to open the files specified in order to retrieve your newly defined passwords, you would then use these passwords to login to the web ui and/or reconfigure 3rd party apps to use the new rpc2 credentials.

If you want to specify your own passwords then please read Q4 below.

**Q4.** OK i have identified (via Q3) i need to define my credentials for the web ui and/or rpc2, how do i do this so that i dont have to use the randomly generated passwords?

**A4.** In order to set your own passwords for the web ui and/or rpc2 you need to create additional environment variables (if not already present), 

The 'key' names will be:-

```
WEBUI_PASS
```

and

```
RPC2_PASS
```

The 'value' for each of the above will be the password you want to set.

**NOTE** unRAID users - Creating environment variables is done by left clicking the icon shown in the Docker tab in the unRAID web ui, select 'Edit', click on 'Add another Path, Port, Variable, Label or Device' then select 'Config Type' of 'Variable' and define the key and value as shown above.