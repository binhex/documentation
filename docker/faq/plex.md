**Plex FAQ**

Q1. How do i configure Plex to transcode to RAM/Array/Cache?

A1. Below are the different options for setting transcoding for Plex and Plex Pass:-

**Transcode to RAM** - create a new volume mapping, host path /tmp and container path /transcode then define TRANS_DIR so that it points at ram drive e.g.:-

```TRANS_DIR=/transcode```

```/transcode```

maps to host path

```/tmp```

**Transcode to the array** - create a new unRAID user share named 'Transcode' (or whatever you want) and then define TRANS_DIR so that it points at your array e.g.:-

```TRANS_DIR=/transcode```

```/transcode```

maps to host path

```/mnt/user/Transcode```

**Transcode to cache** (preferably cache is SSD not spinner) - define TRANS_DIR so that it points at your cache drive e.g.:-

```TRANS_DIR=/transcode```

```/transcode```

maps to host path

```/config/tmp```

**Note**:- Recommended transcode method is to use the cache drive.

Q2. How do i configure Plex to use my GPU for encoding/decoding (sometimes referred to as hardware transcoding)?

A2. The best way to see how to achieve this is by watching the excellent YouTube video by SpaceInvader One, link below:-

https://www.youtube.com/watch?v=GOhHiFAXwOE