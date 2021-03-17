# **qBittorrentVPN Docker FAQ**

**Q1.** The default username and password to access qBittorrent Web UI doesn't work, what could be the cause of this?

**A1.** If you are attempting to access the qBittorrent Web UI from a Windows host then the issue maybe caused by BitDefender, this has a 'Online Threat Protection' function that can block access to the UI. In order to fix this you need to add in an exception for the qBittorrent Web UI, once done retry accessing the UI.

**Q2.** In qBittorrent Web UI i cannot get the 'Automatically add torrents from:' option to save the 'monitored Folder' path, whenever i enter the path and click 'Save' button at the bottom it reverts the change, why is this happening and how can i fix it?.

**A2.** So qBittorrent seems to be a bug in the Web UI where certain paths are not saved, its still an open bug on Github (https://github.com/qbittorrent/qBittorrent/issues/7200) but there is a workaround for this, although the fix is a little odd!.

1. Open qBittorrent Web UI and click on the cog (options) and select the 'Downloads' tab
2. Type in **PART** of the path for your watched folder, for instance if you want your monitored folder to be ```/data/qbittorrent/watched```, then enter in ```/data``` then click on the tiny file icon to the right of the 'Override Save Location' field, then click on 'Save' at the bottom of the window.
3. Repeat step 1. modifying the monitored path by expanding the path one folder at a time and saving until the entire path is populated.
4. Test monitored folder by dropping in a .torrent file and watching the qBitorrent Web UI to ensure its picked up and loaded.