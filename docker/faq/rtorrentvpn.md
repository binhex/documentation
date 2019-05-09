# **rTorrentVPN Docker FAQ**

**Q1.** Why are some of my settings reverting when i restart rutorrent?

**A1.** So rutorrent is purely a web frontend to rtorrent, and as such does NOT modify any settings for rtorrent, the only settings you can save using rutorrent are settings for rutorrent itself, i.e. things like enabling/disabling plugins, settings for plugins etc.

If you want to modify things like incoming port, enabling/disabling dht, and folders for incomplete/complete downloads then you will have to modify the rtorrent config file, this is located in ```/config/rtorrent/config/rtorrent.rc``` please make sure you use something like notepad++ (not windows notepad) to prevent the line endings being modified.
