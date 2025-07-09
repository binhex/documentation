# **qBittorrentVPN Docker FAQ**

**Q1.** The default username and password to access the qBittorrent Web UI don't work. What could be the cause of this?

**A1.** If you are attempting to access the qBittorrent Web UI from a Windows host, then the issue may be caused by BitDefender. This has an 'Online Threat Protection' function that can block access to the UI. In order to fix this, you need to add an exception for the qBittorrent Web UI. Once done, retry accessing the UI.

**Q2.** In the qBittorrent Web UI, I cannot get the 'Automatically add torrents from:' option to save the 'Monitored Folder' path. Whenever I enter the path and click the 'Save' button at the bottom, it reverts the change. Why is this happening, and how can I fix it?

**A2.** There seems to be a bug in the qBittorrent Web UI where certain paths are not saved. It's still an open bug on GitHub (https://github.com/qbittorrent/qBittorrent/issues/7200), but there is a workaround for this, although the fix is a little odd!

1. Open the qBittorrent Web UI, click on the cog (options), and select the 'Downloads' tab.
2. Type in **PART** of the path for your watched folder. For instance, if you want your monitored folder to be `/data/qbittorrent/watched`, then enter `/data`, then click on the tiny file icon to the right of the 'Override Save Location' field, then click on 'Save' at the bottom of the window.
3. Repeat step 1, modifying the monitored path by expanding the path one folder at a time and saving until the entire path is populated.
4. Test the monitored folder by dropping in a .torrent file and watching the qBittorrent Web UI to ensure it's picked up and loaded.

**Q3.** I am seeing fluctuating download and upload speeds with qBittorrent. What can I do to make the speeds more stable?

**A3.** One setting that does seem to make a difference is to change the 'Peer connection protocol' from 'TCP and uTP' to 'TCP'. This setting can be found in qBittorrent Web UI > Options > Connection tab > Peer Connection protocol.

**Q4.** I need to run the qBittorrent Web UI on a different port as port 8080 is conflicting with an existing container/service. I have changed the host side of the port, but this does not seem to have worked and I cannot now access the qBittorrent Web UI. Why is this, and how can I fix it?

**A4.** qBittorrent has built-in security measures (CSRF protection) to ensure that the port you are requesting from (host port) and the port qBittorrent is listening on (container port) are the same. However, due to this running in a Docker container, this may not always be the case, especially when you want to change the port as it conflicts with an existing application. This leads to how to fix this issue:

1. Open Unraid Web UI/Docker, left-click qBittorrent, and select `Edit`.
2. Go to `Container Variable: WEBUI_PORT`, click on `Edit`, and set the `Value` to the port number you want to run the Web UI on, then click `Save`.
3. Find `Container Port: 8080` and click on `Remove`.
4. Click on the blue link `Add another Path, Port, Variable, Label or Device` and select `Config Type` of `Port`.
5. Set the `Container Port:` and `Host Port:` to the same value as the `Container Variable: WEBUI_PORT`, then click on `Add`.
6. Click on `Apply` at the bottom and wait for the container to be recreated.
7. Attempt access to the qBittorrent Web UI using the new port.