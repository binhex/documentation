# **qBittorrentVPN Docker FAQ**

**Q1.** The default username and password to access qBittorrent Web UI doesn't work, what could be the cause of this?

**A1.** If you are attempting to access the qBittorrent Web UI from a Windows host then the issue maybe caused by BitDefender, this has a 'Online Threat Protection' function that can block access to the UI. In order to fix this you need to add in an exception for the qBittorrent Web UI, once done retry accessing the UI.
