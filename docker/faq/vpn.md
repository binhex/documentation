# **VPN Docker FAQ**

**Q1.** Do you implement a kill switch to prevent IP leakage when the VPN tunnel goes down?

**A1.** No i do not implement a kill switch, what i do implement is better, let me explain...

The VPN Docker images i produce use iptables (firewall) to prevent IP leakage at ALL times by using blocking rules, thus ensuring whatever state the VPN tunnel is in (up, down or otherwise) IP leakage cannot occur.

Kill switches on the other hand only block AFTER the VPN tunnel has gone down, thus potentially leaving a time gap between tunnel being down and the kill switch kicking in and blocking the connection, during this time window it is potentially possible for ip leakage to occur.

If the tunnel does happen to go down then openvpn will automatically reconnect, if the openvpn process dies (crashes) then the process will be automatically started thus ensuring at all times a constant connection and zero leakage.

**Q2.** I can't seem to access the webui from outside my LAN, why is this?

**A2.** The Docker VPN images use iptables in order to secure against ip leakage of your ISP assigned ip address, this requires all modules loading at the kernel level for iptables, including the iptable_mangle module. If the iptable_mangle module is not loaded/available on your hosts kernel then you will not be able to access the webui outside of your LAN. Until recently unRAID DID include iptable_mangle support by default, but the latest release (6.1.8 or later) has removed this.

In order to force the loading of iptable_mangle you need to add the following to your unRAID "go" file, this can be done by issuing the following:-

1. SSH into the unRAID host and issue the following commands:-

```
echo "# force iptable mangle module to load (required for *vpn dockers)" >> /boot/config/go
echo "/sbin/modprobe iptable_mangle" >> /boot/config/go
```

2. Reboot the host for the change to take effect

Note - If you want to apply the fix straight away issue the following:-

```/sbin/modprobe iptable_mangle```

**Q3.** What is the purpose of Privoxy?

**A3.** Privoxy is a non-caching web proxy with filtering capabilities for enhancing privacy, manipulating cookies and modifying web page data and HTTP headers before the page is rendered by the browser. In practise what this gives you by including it in the same container as the VPN tunnel is that you can bypass any filtering that maybe present by your ISP by simply configuring your browser to use the proxy server.

This is achieved by sending and receiving all data via the VPN tunnel, think of Privoxy as a middle man who will route traffic for you from your LAN over the VPN tunnel and back again.

The other uses as well as simple web browsing is certain applications can also be told to use the proxy when downloading metadata, such as nzb or torrent files from index sites (sickchill, medusa, sonarr, radarr etc all have proxy support), as some ISP's may block certain index sites, this is an extremely useful feature.

The other common use is bypassing Geo-blocking, again normally done through the browser, allowing you to potentially access sites as if you were coming from another country (useful for BBC iPlayer, Netflix etc).

Note:- Privoxy is NOT intended to be used by the application running inside the container (deluge, rutorrent, qbittorrent etc), this is not required and can cause slowdown and/or connection issues.

**Q4.** I'm struggling to configure LAN_NETWORK correctly, can you give some examples?

**A4.** Sure!, here is a list of examples and how to identify the correct CIDR notation (digit(s) after the /)

If you type "ipconfig /all" on windows host on your LAN you will get something similar to this:-

```
Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : home.gateway
   Description . . . . . . . . . . . : Red Hat VirtIO Ethernet Adapter
   Physical Address. . . . . . . . . : 11-22-33-44-55-66
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe00::1111:2222:3333:4444%4(Preferred)
   IPv4 Address. . . . . . . . . . . : 192.168.1.10(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Lease Obtained. . . . . . . . . . : 17 February 2016 21:10:32
   Lease Expires . . . . . . . . . . : 27 February 2016 11:10:13
   Default Gateway . . . . . . . . . : 192.168.1.1
   DHCP Server . . . . . . . . . . . : 192.168.1.1
   DHCPv6 IAID . . . . . . . . . . . : 55727104
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-1D-4A-97-73-52-54-00-32-3F-43
   DNS Servers . . . . . . . . . . . : 193.1.2.3
   NetBIOS over Tcpip. . . . . . . . : Enabled
```

or "ifconfig" on linux:-

```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
        ether 68:05:ca:0b:fe:25  txqueuelen 0  (Ethernet)
        RX packets 28203743  bytes 36171326044 (33.6 GiB)
        RX errors 0  dropped 19925  overruns 0  frame 0
        TX packets 26710466  bytes 165269242671 (153.9 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

So you can see the ip address of this host 192.168.1.10 and the netmask 255.255.255.0, so the internal network is defined as:-

```192.168.1.0```

and the netmask in CIDR format is:-

```/24```

Some common netmask to CIDR notation examples:-

```
255.255.255.0 = /24
255.255.0.0 = /16
255.0.0.0 = /8
```

**Q5.** I've just updated and now the container won't start. If i look in the /config/supervisord.log file i see the message below, what does it mean and how do i fix it?.

```VERIFY ERROR: depth=0, error=CA signature digest algorithm too weak:```

**A5.** The above message is informing you that the cipher used for the VPN providers certificate is too weak and thus susceptible to decryption by a third party. In order to fix this you need to contact your VPN provider and inform them that the certificate available is using a weak cipher and has been flagged as such by OpenSSL 1.1.x and kindly ask them to re-generate a new certificate with a stronger cipher.

Keep in mind the purpose of a VPN is to provider secure, anonymous connectivity to the internet, using a weak cipher means you are potentially exposing the connection to snooping.

**Q6.** My download/upload speeds are low when connected to the VPN tunnel, what could be the cause of this?.

**A6.** There are multiple potential causes for low speeds, here is a list of the common ones:-

Incoming port not defined correctly - This is the main cause of low speeds, if you want to maintain high upload/download rates then you MUST have a working incoming port. If you are using PIA as your VPN provider then this will be done for you automatically, as long as you are connected to a endpoint that supports port forwarding (see list below) AND STRICT_PORT_FORWARD is set to "yes". If you are using another VPN provider then you will need to find out if your VPN provider supports port forwarding and what mechanism they use to allocate the port, and finally configure the application to use the port.

PIA endpoints that support port forwarding (incoming port) can be seen here:-

https://www.privateinternetaccess.com/helpdesk/kb/articles/how-do-i-enable-port-forwarding-on-my-vpn

Upload rate set too high/unlimited - failure to correctly define your upload speed will mean your connection will be choked, resulting in low download speeds, the solution to this is to define your upload rate as about 3/4 of your theoretical maximum upload rate (keeping in mind this is defined in Bytes (big B) NOT bits (small b).

(VPN provider specific) Use GCM cipher instead of CBC - If the VPN provider you are using supports AES-128-GCM/AES-256-GCM (PIA does support this) then by switching to GCM you can improve security (stronger cipher), reduce CPU load and potentially improve dl/ul speeds when compared to using traditional ciphers such as CBC. To achieve this you simply edit the ovpn configuration file located in /config/openvpn/ and include the following lines, once saved restart the container:-
cipher aes-128-gcm
auth sh**A256
ncp-disable
Note - Please ensure you remove any other existing lines that may clash with the lines above, 

(Deluge specific) Disable in/out utp - There have been reports of significant speed increases by disabling utp, this can be achieved by installing the Deluge plugin 'itconfig', once installed make sure to disabled both 'enable_incoming_utp' and 'enable_outgoing_utp', then restart the container. 

(Deluge specific) Rate limit overhead enabled - If the option in the Deluge Web UI in the "Bandwidth" section labelled "Rate limit IP overhead" is ticked this can result in low speeds, please untick this option.

VPN endpoint has low bandwidth - Not all VPN endpoints are equal, some will have larger allocations of bandwidth than others, you will need to check with your VPN provider to identify which are the faster endpoints and connect to one of these.

Highly fragmented disk - If your disk has a lot of fragmentation then speeds can be low due to the significantly reduced I/O that a fragmented disk can cause. This can be fixed by performing a disk defrag to optimise the disk.

Name Resolution not working - When specifying the NAME_SERVER value you must keep in mind that your ISP's Name Servers will most probably block name resolution for everything that doesn't have a source IP address owned by them, thus when the VPN tunnel is established Name Resolution will most likely fail if you're using your ISP's Name Servers (as your source IP will be different) and this will result in low speeds. The fix for this is to use Name Servers which permit usage from ANY source IP, such as Google or FreeDNS, both of which are set by default.

Writing incomplete/partial downloads directly to the unRAID array - unRAID writes to the array are normally slow, typically being in the range 20MB/s to 50MB/s depending on hardware. Due to this low write speed you may see issues when a torrent client attempts to write incomplete/partial downloads directly to the array, especially when multiple writes from different downloads are in progress. The fix is to write to a cache drive (preferably SSD) or use the 'Unassigned Devices' plugin to connect to a single drive (again preferably SSD)  - see below for details on this.

There have been reports from users that using a unRAID mirrored cache pool (has to be BTRFS currently) causes yo-yo download speeds and/or timeouts to the download daemon (seen with rTorrentVPN), there is currently no fix to this other than to break the mirror and use a single cache drive. The alternative method is to use the unRAID plugin 'unassigned devices' and mount an external drive and use this to store incomplete/completed downloads on. The other point to mention is that XFS seems to be a more efficient and stable filesystem in comparison to BTRFS (at this time), so if you do split your cache pool it maybe worth taking the opportunity to switch to XFS, as you will need to format in any case after the mirror split.

Consumer grade routers are notoriously underpowered with slow processors and very little RAM, this can result in low or fluctuating download/upload speeds. This is particular true when torrenting, as this causes a heavy burden on the router due to multiple (sometimes hundreds) connections from peers. The solution to this is either simply swap out the router with something more powerful (such a pfSense box) or reduce the number of connections, either globally or per torrent in the torrent client.

**Q7.** Since the update i can't now start the container when VPN_ENABLED is set to 'yes' and i see the following message in /config/supervisord.log

"[crit] No OpenVPN config file located in /config/openvpn/ (ovpn extension), please download from your VPN provider and then restart this container, exiting..." what does this mean and how can i fix this?

**A7.** Recently i have stpped baking in the OpenVPN configuration file and certs for PIA users (there are multiple reasons for this which i won't go into here), so in order to create a tunnel to your VPN provider you now need to download their OpenVPN configuration file and certificates. These will typically be downloaded from your VPN providers website, and generally are zipped.

PIA users - The URL to download the openvpn configuration files and certs is https://www.privateinternetaccess.com/openvpn/openvpn.zip

Once you have downloaded the zip (normally a zip as they contain multiple ovpn files) then extract it to /config/openvpn/ folder (if that folder doesn't exist then start and stop the docker container to force the creation). If there are multiple ovpn files then please delete the ones you don't want to use (normally filename follows location of the endpoint) leaving just a single ovpn file and the referenced certificates (normally files with a crt and pem extension).

**Q8.** I'm unable to see the Web UI and i'm seeing the following in the /config/supervisord.log file, what does this mean and how can i fix this?

```Linux ip -6 addr add failed: external program exited with error status: 2```

**A8.** This is due to the VPN provider pushing an OpenVPN option to use IPv6 to the client (your end), due to the fact that unRAID 6.3.x or earlier doesn't support IPv6 you will then see the above error message. To prevent this we can filter out the pushed options by adding the following lines to your ovpn file (located in /config/openvpn/<your filename>.ovpn)

```
pull-filter ignore "route-ipv6"
pull-filter ignore "ifconfig-ipv6"
```

Save the file and restart the container for the change to take effect.

**Q9.** How can i confirm that my incoming port is working when the VPN tunnel is established?

**A9.** To do this you can use the website https://www.yougetsignal.com/tools/open-ports/ this allows you to put in your public IP address for your VPN connection (can be found in the /config/supervisord.log) and the incoming port that you have manually configured (or in the case of PIA auto configured) for the application. Once you have entered in these details hit the "check" button to confirm the port is open.

**Q10.** I can't connect to the Web UI and i see the following repeated over and over in the logs, what does mean and how do i fix it?

```
2018-04-02 21:13:42,659 DEBG 'start-script' stdout output:
[warn] Response code 000 from curl != 2xx
[warn] Exit code 7 from curl != 0
[info] 4 retries left
[info] Retrying in 10 secs...
```

**A10.** The above message means that you have env var 'STRICT_PORT_FORWARD' set to a value of 'yes' but the endpoint you are connecting to does NOT support port forwarding. For a current list of port forward enabled endpoints for PIA, see the link below:-

https://www.privateinternetaccess.com/helpdesk/kb/articles/how-do-i-enable-port-forwarding-on-my-vpn

The alternative to this is to set env var 'STRICT_PORT_FORWARD' value to 'no', this will then skip any port forwarding and allow you to connect to ANY PIA endpoint independent of whether it supports port forwarding or not (not recommended).

Note:- The above is ONLY true for PIA users, the env var 'STRICT_PORT_FORWARD' does nothing for any other VPN providers.

**Q11.** I have an application that does not seem to support the use of a proxy (privoxy), how do i configure the application to use Privoxy?

**A11.** You can configure ANY application to use Privoxy by adding in the following environment variable to the "Extra Parameters" field (please switch to "Advanced view" to view this) in the unRaid Web UI/Docker tab.

```-e RUN_OPTS="--ProxyConnection=<host ip addr>:<privoxy host port number>"```

**Q12.** I can see from the '/config/supervisord.log' file that the openvpn process keeps getting killed every 30 seconds on my QNAP appliance, what could be the cause of this?

**A12.** For some reason (unknown at this time) QNAP decided to kill any openvpn process running on the host by adding in a line to the 'daemon_mgr.conf' file. In order to prevent this you need to delete the following line from the 'daemon_mgr.conf':-

```DAEMONxx = openvpn, stop, /usr/sbin/openvpn```

Where xx will be 2 random digits.

**Q13.** I am a Synology DS-1817+ user and am seeing very bad download/upload speeds whilst using DelugeVPN/SABnzbdVPN/qBittorrentVPN, what could be the cause?.

**A13.** The Synology DS-1817+ can have performance issues when running dockers that include openvpn client, this can manifest itself as slow download/upload rates. There are two solutions to this problem, either reduce the load on the system by shutting down other containers/vm's or alternatively by running the vpn enabled docker container on a more powerful system.

**Q14.** I have setup port forwarding on my router/firewall but still cannot seem to seed any torrents and my incoming port is showing as closed, how can i fix this?

**A14.** A common misconception is that port forwarding for a torrent client when using a VPN connection is still done on the users router/firewall, this is NOT the case. In order to have a working incoming port you need to have all of the following in place:-

1. The VPN provider you have signed up to provides port forwarding - For PIA users this is the case, there are other providers too which allow port forwarding, but be aware most do NOT, check with the provider BEFORE signing up.

2. You have an assigned incoming port from your VPN provider - For PIA users this is done automatically for you, so as long as you are connected to an endpoint that supports port forwarding (see here for details) AND you have STRICT_PORT_FORWARD  set to yes. For other providers they may manually assign a port for you on request, or may use other mechanisms.

3. You have configured the application to use the assigned port - For PIA users this is done automatically for you, for other providers you will need to manually set the application to use the port assigned to you.

**Q15.** I am seeing the following in the log file '/config/supervisord.log' and cannot access the web ui, what does it mean and how can i fix it?.

```AUTH: Received control message: AUTH_FAILED'```

**A15.** This error means that the credentials you have entered for environment variables 'VPN_USER' and 'VPN_PASS' are not valid, and thus your VPN provider is not allowing you to connect. Please re-check the values for both of these environment variables is correct, also try typing in the value for both as opposed to copy/paste, as this can lead to unexpected characters and thus authentication failure.