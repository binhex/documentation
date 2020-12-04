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

**A4.** Sure!, a common misconception when defining this is to set the IP address to the value of your router or host (server), this is NOT correct. What you need to do is set the value to encapsulate all host IP addresses for your home network, NOT for a particular host on the network. Below is an examples of how to identify the correct IP address and CIDR notation (digit(s) after the /)

If you type "ipconfig /all" on Windows host on your LAN you will get something similar to this:-

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

or "ifconfig" on Linux/Mac:-

```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
        ether 68:05:ca:0b:fe:25  txqueuelen 0  (Ethernet)
        RX packets 28203743  bytes 36171326044 (33.6 GiB)
        RX errors 0  dropped 19925  overruns 0  frame 0
        TX packets 26710466  bytes 165269242671 (153.9 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

So armed with the IP Address and Subnet Mask, the easiest way to work out the correct values is to use a online calculator, go to:- http://www.subnet-calculator.com/cidr.php

1. Enter the host IP address into the 'IP Address' field, in this example '192.168.1.10'
2. On the dropdown 'CIDR Netmask' select your subnet mask, in this example 255.255.255.0
3. Copy the value from 'Net: CIDR Notation', which in this example would be 192.168.1.0/24
4. Paste it into the value for env var 'LAN_NETWORK'

**Q5.** I've just updated and now the container won't start. If i look in the /config/supervisord.log file i see the message below, what does it mean and how do i fix it?.

```VERIFY ERROR: depth=0, error=CA signature digest algorithm too weak:```

**A5.** The above message is informing you that the cipher used for the VPN providers certificate is too weak and thus susceptible to decryption by a third party. In order to fix this you need to contact your VPN provider and inform them that the certificate available is using a weak cipher and has been flagged as such by OpenSSL 1.1.x and kindly ask them to re-generate a new certificate with a stronger cipher.

Keep in mind the purpose of a VPN is to provider secure, anonymous connectivity to the internet, using a weak cipher means you are potentially exposing the connection to snooping.

**Q6.** My download/upload speeds are low when connected to the VPN tunnel, what could be the cause of this?.

**A6.** There are multiple potential causes for low speeds, here is a list of the common ones:-

* Incoming port not defined correctly - This is the main cause of low speeds, if you want to maintain high upload/download rates then you MUST have a working incoming port. If you are using PIA as your VPN provider then this will be done for you automatically, as long as you are connected to a endpoint that supports port forwarding (see list below) AND STRICT_PORT_FORWARD is set to "yes". If you are using another VPN provider then you will need to find out if your VPN provider supports port forwarding and what mechanism they use to allocate the port, and finally configure the application to use the port.

PIA endpoints that support port forwarding (incoming port) can be seen in the log file ```/config/supervisord.log```

* Upload rate set too high/unlimited - failure to correctly define your upload speed will mean your connection will be choked, resulting in low download speeds, the solution to this is to define your upload rate as about 3/4 of your theoretical maximum upload rate (keeping in mind this is defined in Bytes (big B) NOT bits (small b).

* (VPN provider specific) Use GCM cipher instead of CBC - If the VPN provider you are using supports AES-128-GCM/AES-256-GCM (PIA does support this) then by switching to GCM you can improve security (stronger cipher), reduce CPU load and potentially improve dl/ul speeds when compared to using traditional ciphers such as CBC. To achieve this you simply edit the ovpn configuration file located in /config/openvpn/ and include the following lines, once saved restart the container:-
cipher aes-128-gcm
auth sh**A256
ncp-disable
Note - Please ensure you remove any other existing lines that may clash with the lines above, 

* (Deluge specific) Disable in/out utp - There have been reports of significant speed increases by disabling utp, this can be achieved by installing the Deluge plugin 'itconfig', once installed make sure to disabled both 'enable_incoming_utp' and 'enable_outgoing_utp', then restart the container. 

* (Deluge specific) Rate limit overhead enabled - If the option in the Deluge Web UI in the "Bandwidth" section labelled "Rate limit IP overhead" is ticked this can result in low speeds, please untick this option.

* VPN endpoint has low bandwidth - Not all VPN endpoints are equal, some will have larger allocations of bandwidth than others, you will need to check with your VPN provider to identify which are the faster endpoints and connect to one of these.

* Highly fragmented disk - If your disk has a lot of fragmentation then speeds can be low due to the significantly reduced I/O that a fragmented disk can cause. This can be fixed by performing a disk defrag to optimise the disk.

* Name Resolution not working - When specifying the NAME_SERVER value you must keep in mind that your ISP's Name Servers will most probably block name resolution for everything that doesn't have a source IP address owned by them, thus when the VPN tunnel is established Name Resolution will most likely fail if you're using your ISP's Name Servers (as your source IP will be different) and this will result in low speeds. The fix for this is to use Name Servers which permit usage from ANY source IP, such as Google or FreeDNS, both of which are set by default.

* Writing incomplete/partial downloads directly to the unRAID array - unRAID writes to the array are normally slow, typically being in the range 20MB/s to 50MB/s depending on hardware. Due to this low write speed you may see issues when a torrent client attempts to write incomplete/partial downloads directly to the array, especially when multiple writes from different downloads are in progress. The fix is to write to a cache drive (preferably SSD) or use the 'Unassigned Devices' plugin to connect to a single drive (again preferably SSD)  - see below for details on this.

* There have been reports from users that using a unRAID mirrored cache pool (has to be BTRFS currently) causes yo-yo download speeds and/or timeouts to the download daemon (seen with rTorrentVPN), there is currently no fix to this other than to break the mirror and use a single cache drive. The alternative method is to use the unRAID plugin 'unassigned devices' and mount an external drive and use this to store incomplete/completed downloads on. The other point to mention is that XFS seems to be a more efficient and stable filesystem in comparison to BTRFS (at this time), so if you do split your cache pool it maybe worth taking the opportunity to switch to XFS, as you will need to format in any case after the mirror split.

* Consumer grade routers are notoriously underpowered with slow processors and very little RAM, this can result in low or fluctuating download/upload speeds. This is particular true when torrenting, as this causes a heavy burden on the router due to multiple (sometimes hundreds) connections from peers. The solution to this is either simply swap out the router with something more powerful (such a pfSense box) or reduce the number of connections, either globally or per torrent in the torrent client.

**Q7.** Since the update i can't now start the container when VPN_ENABLED is set to 'yes' and i see the following message in /config/supervisord.log

"[crit] No OpenVPN config file located in /config/openvpn/ (ovpn extension), please download from your VPN provider and then restart this container, exiting..." what does this mean and how can i fix this?

**A7.** Recently i have stpped baking in the OpenVPN configuration file and certs for PIA users (there are multiple reasons for this which i won't go into here), so in order to create a tunnel to your VPN provider you now need to download their OpenVPN configuration file and certificates. These will typically be downloaded from your VPN providers website, and generally are zipped.

PIA users - The URL to download the openvpn configuration files and certs is https://www.privateinternetaccess.com/openvpn/openvpn-nextgen.zip

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

**A10.** The above message means that you have env var 'STRICT_PORT_FORWARD' set to a value of 'yes' but the endpoint you are connecting to does NOT support port forwarding (PIA users only). Please keep in mind PIA may remove or add endpoints that support port forwarding at **ANY** time, this is out of my control.

For a dynamically generated up to date list of port forward enabled endpoints for PIA, see your log located at /config/supervisord.log.

The below is an example snippet from the log, please do **NOT** use this as the current list:-

```
2020-03-25 17:05:32,603 DEBG 'start-script' stdout output:
[info] PIA endpoint 'austria.privacy.network' is in the list of endpoints that support port forwarding

2020-03-25 17:05:32,603 DEBG 'start-script' stdout output:
[info] List of PIA endpoints that support port forwarding:-
[info] al.privacy.network
[info] ad.privacy.network
[info] austria.privacy.network
[info] brussels.privacy.network
[info] ba.privacy.network
[info] sofia.privacy.network
[info] czech.privacy.network
[info] denmark.privacy.network
[info] ee.privacy.network
[info] fi.privacy.network
[info] france.privacy.network
[info] de-berlin.privacy.network
[info] de-frankfurt.privacy.network
[info] gr.privacy.network
[info] hungary.privacy.network
[info] is.privacy.network
[info] ireland.privacy.network
[info] man.privacy.network
[info] italy.privacy.network
[info] lv.privacy.network
[info] liechtenstein.privacy.network
[info] lt.privacy.network
[info] lu.privacy.network
[info] mk.privacy.network
[info] malta.privacy.network
```

The alternative to this is to set env var 'STRICT_PORT_FORWARD' value to 'no', this will then skip any port forwarding and allow you to connect to ANY PIA endpoint independent of whether it supports port forwarding or not (not recommended as speeds will be slow).

Note:- The above is ONLY true for PIA users, the env var 'STRICT_PORT_FORWARD' does nothing for any other VPN providers.

**Q11.** I see from Q10 that i need to change the PIA endpoint i connect to due to the endpoint im currently connected to be being disabled for port forwarding, so how do i do this?

**A11.** There are two ways of switching the endpoint you connect to:-

**Method 1. (recommended)**
Download the latest ovpn zip pack from PIA, link below:-
https://www.privateinternetaccess.com/openvpn/openvpn-nextgen.zip

Extract the zip to /config/openvpn/ and then delete all .ovpn files that you do NOT want to connect to, leaving the ovpn file you want to use and the required certificates, then restart the container for the change to take effect.

**Method 2.**
Download a text editor that honors line endings, such as notepad++ or atom, then open the file /config/openvpn/`<filename with a ovpn extension>`

Look for the line that starts with 'remote' and modify this line to one of the port forward enabled endpoints shown in your log (see Q10 for details on how to find this).

Example, switching from 'italy' to 'malta':-

```
remote italy.privacy.network 1198 udp
```
to
```
remote malta.privacy.network 1198 udp
```

Save the file and restart the container for the change to take effect.

**Q12.** I have an application that does not seem to support the use of a proxy (privoxy), how do i configure the application to use Privoxy?

**A12.** You can configure ANY application to use Privoxy by adding in the following environment variable to the "Extra Parameters" field (please switch to "Advanced view" to view this) in the unRaid Web UI/Docker tab.

```-e RUN_OPTS="--ProxyConnection=<host ip addr>:<privoxy host port number>"```

**Q13.** I can see from the '/config/supervisord.log' file that the openvpn process keeps getting killed every 30 seconds on my QNAP appliance, what could be the cause of this?

**A13.** For some reason (unknown at this time) QNAP decided to kill any openvpn process running on the host by adding in a line to the 'daemon_mgr.conf' file. In order to prevent this you need to delete the following line from the 'daemon_mgr.conf':-

```DAEMONxx = openvpn, stop, /usr/sbin/openvpn```

Where xx will be 2 random digits.

**Q14.** I am a Synology DS-1817+ user and am seeing very bad download/upload speeds whilst using DelugeVPN/SABnzbdVPN/qBittorrentVPN, what could be the cause?.

**A14.** The Synology DS-1817+ can have performance issues when running dockers that include openvpn client, this can manifest itself as slow download/upload rates. There are two solutions to this problem, either reduce the load on the system by shutting down other containers/vm's or alternatively by running the vpn enabled docker container on a more powerful system.

**Q15.** I have setup port forwarding on my router/firewall but still cannot seem to seed any torrents and my incoming port is showing as closed, how can i fix this?

**A15.** A common misconception is that port forwarding for a torrent client when using a VPN connection is still done on the users router/firewall, this is NOT the case, port forwarding MUST be done on the VPN provider side only. In order to have a working incoming port you need to have all of the following in place:-

* The VPN provider you have signed up to provides port forwarding - For PIA users this is the case, there are other providers too which allow port forwarding, but be aware most do NOT, check with the provider BEFORE signing up.

* You have an assigned incoming port from your VPN provider - For PIA users this is done automatically for you, so as long as you are connected to an endpoint that supports port forwarding (see here for details) AND you have STRICT_PORT_FORWARD  set to yes. For other providers they may manually assign a port for you on request, or may use other mechanisms.

* You have configured the application to use the assigned port - For PIA users this is done automatically for you, for other providers you will need to manually set the application to use the port assigned to you.

**Q16.** I am seeing the following in the log file '/config/supervisord.log' and cannot access the web ui, what does it mean and how can i fix it?.

```AUTH: Received control message: AUTH_FAILED'```

**A16.** AUTH_FAILED means you are having issues authenticating with your VPN provider, there can be many causes for this, here are some of the common ones:-

* [Cause] Your subscription has run out [Solution] double check this on the vpn providers website.

* [Cause] You have not typed in your username (VPN_USER value) and/or password (VPN_PASS value) correctly [Solution] do not copy and paste, type it in manually to prevent whitespace issues.

* [Cause] You are using the wrong credentials [Solution] ensure the credentials are for openvpn/wireguard, NOT proxy servers etc. [**PIA users**] Do **NOT** use the generated PPTP/L2TP/Socks Username and Password, this is not the correct credentials, you need to specify the web login credentials (username will be of the format Pxxxxxx) for VPN_USER and VPN_PASS.

* [Cause] Your password contains a character which may cause issues [Solution] please ensure it only contains letters a-z (upper case or lower case) and numbers 0-9.

* [Cause] Your password is too long [Solution] Certain VPN providers (such as PIA) may limit the password length, please try shortening the password for your account. [**PIA users**] The maximum length for account passwords has changed with next-gen network, please ensure your password is 99 characters or less.

* [Cause] Out of date openvpn config file (ovpn extension) [Solution] ensure you download the latest ovpn file from your vpn provider.

* [Cause] The vpn provider you have signed up with is having authentication issues [Solution] try another endpoint, failing that contact the vpn provider and explain you are having authentication issues when using native openvpn/wireguard clients with AUTH_FAILED shown.

**Q17.** I'm unable to connect to the web ui and i'm seeing the following repeated over and over in the /config/supervisord.log file, what does it mean and how can i fix it?

```
2020-02-04 07:21:26,213 DEBG 'start-script' stdout output:
Tue Feb  4 07:21:26 2020 [UNDEF] Inactivity timeout (--ping-restart), restarting

2020-02-04 07:21:26,213 DEBG 'start-script' stdout output:
Tue Feb  4 07:21:26 2020 SIGHUP[soft,ping-restart] received, process restarting
```

**A17.** This means the OpenVPN Client is unable to connect to the VPN providers server on the specified IP address and port (as defined in the ovpn file 'remote' line), this can have many causes, some of the more common causes (and solutions) are as follows:-

* [Cause] Hardware firewall/router is blocking outbound connections to the vpn providers servers. [Solution] Allow outbound connections for the port defined in the VPN providers ovpn file on your firewall/router.

* [Cause] Host machine firewall blocking the docker container from connecting to the vpn providers servers. [Solution] Allow outbound connections for the port defined in the VPN providers ovpn file on the hosts firewall.

* [Cause] VLAN blocking the connection for the host to the VPN providers servers. [Solution] Allow outbound connections for the port defined in the VPN providers ovpn file on your switch.

* [Cause] Out of date ovpn config file containing reference to retired VPN remote server(s). [Solution] Download the latest ovpn config file from your VPN provider, place in /config/openvpn/ and restart container.

* [Cause] ISP is hijacking DNS lookup and redirecting you to their (spammy) landing page. [Solution] Use an IP based ovpn config file instead, this wil circumvent the requirement to do a name lookup.

* [Cause] ISP is blocking outbound connections to the VPN providers servers. [Solution] Contact VPN provider to confirm outage and wait for the outage to be resolved.

* [Cause] VPN provider has a major outage. [Solution] Allow outbound connections for the port defined in the VPN providers ovpn file on your firewall/router.

Once you have ruled out any potential Home LAN issues and if none of the above resolve the issue then you may have to switch VPN provider or even ISP to get around the blocking restriction.

**Q18.** When i set my application (such as Sonarr, Radarr, etc) to use Privoxy and do a curl/wget from within the applications container i see that my IP address is my ISP's assigned IP address and NOT the expected VPN provider IP address for the endpoint im connected to, why is this, is the VPN not working correctly?.

**A18.** A proxy server works at a application level NOT a system level, therefore when using command line tools like curl or wget these applications would need to be configured to use the proxy in order to correctly route through and show the VPN provider allocated IP address.

Whereas a VPN client works at the system level, thus all traffic is routed over the VPN tunnel, so using command line utilities such as curl or wget inside the VPN docker container (e.g. DelugeVPN, PrivoxyVPN, etc) WOULD correctly show the VPN allocated IP address.

**Q19.** I see that PIA has a new network called 'Next-Gen', does *VPN Docker Images that you produce support this, and if so how do i switch over to it?

**A19.** Yes, it's now fully supported including port forwarding, if you want to switch from PIA's current network to the 'next-gen' network then please generate a new ovpn file using the following procedure:-

1. Please make sure you have the latest Docker Image by issuing a docker pull.

2. Download next-gen ovpn config file from the following link:-
https://www.privateinternetaccess.com/openvpn/openvpn.zip

3. Extract the zip and copy **ONE** of the ovpn files and any other certs etc to /config/openvpn/, ensuring you either rename the extension or delete the old current-gen network ovpn file.

4. Restart the container and monitor /config/supervisord.log file for any issues.

**Q20.** I would like to specify multiple endpoints to attempt to connect to in case one or more of them have transient issues, can your *VPN Docker images do this, and if so, how?

**A20** Yes, all the Docker Images i produce do support multiple endpoints, this is achieved by editing the OpenVPN configuration file located in /config/openvpn/ and adding in additional 'remote' lines, an example is shown below:-

```
remote al.privacy.network 1198
remote ad.privacy.network 1198
remote austria.privacy.network 1198
remote brussels.privacy.network 1198
remote ba.privacy.network 1198
remote sofia.privacy.network 1198
```

The order shown above will be the order tried, if an endpoint fails to connect then it will try the next, and so on, if it gets to the end of the list then it will start from the top again in a round robin fashion.

**Note** Multiple OpenVPN configuration files is **NOT** supported, only multi remote lines as shown above.

**Q21.** I now see that you support WireGuard, how do i switch from OpenVPN to WireGuard client?

**A21.** Yes you are correct, all binhex VPN created images now support OpenVPN and WireGuard, for PIA and other VPN providers. 

If you're a PIA user then please follow this procedure:-

1. Change Docker parameter from ```--cap-add=NET_ADMIN``` to ```--privileged=true``` (WireGuard requires privileged permissions).
2. Add environment variable and set the ```Key:``` (**NOT** the name) to ```VPN_CLIENT``` and set the ```Value:``` to ```wireguard```.
3. Start the container with the new parameters.
4. Once the container has started you should then be able to see the dynamically generated WireGuard config file ```/config/wireguard/wg0.conf```.
5. If you wish to change the endpoint (default is Netherlands) then open the file ```/config/wireguard/wg0.conf``` and change the ```Endpoint``` line to the endpoint you want to connect to.

If you're a 'custom or airvpn' VPN user (non PIA) then please follow this procedure:-

1. Change Docker parameter from ```--cap-add=NET_ADMIN``` to ```--privileged=true``` (WireGuard requires privileged permissions).
2. Add environment variable and set the ```Key:``` (**NOT** the name) to ```VPN_CLIENT``` and set the ```Value:``` to ```wireguard```.
3. Start and stop the container to force the creation of ```/config/wireguard/```.
4. Copy and paste in the WireGuard configuration file for your VPN provider.
5. Start the container and monitor the log ```/config/supervisord.log``` to ensure the connection is established.

**Q22.** I see the following in the log /config/supervisord.log, what does it mean and how can i fix it?

```
OPTIONS ERROR: failed to negotiate cipher with server. Add the server's cipher ('BF-CBC') to --data-ciphers (currently 'AES-256-GCM:AES-128-GCM:AES-256-CBC') if you want to connect to this server.
```

**A22.** This error is caused by a misconfiguration of the OpenVPN servers at the VPN providers end (currently affecting PIA customers). The VPN server does not push the supported ciphers via the '--cipher' option and so the client has to assume that the server does not do any cipher negotiation. The client then tries to use the same cipher as the server, but PIA's servers are set incorrectly and report support of cipher ```BF-CBC```, the client tries to switch to that cipher and it fails generating the error.  

The fix for this is to specify a fallback cipher on the client side to a cipher that PIA does support, this is done by editing the file ```/config/openvpn/<file with a ovpn extension>``` and adding/replacing the following lines (use notepad++ **NOT** notepad):-

```
cipher aes-256-gcm
ncp-disable
```

Remove this line (if present):-

```
data-ciphers-fallback aes-256-gcm
```

Save and restart the container for the change to take effect.

**Q23.** I see the following in the log /config/supervisord.log, what does it mean and how can i fix it?

```
RTNETLINK answers: Operation not permitted
Unable to access interface: Operation not permitted
[#] ip link delete dev wg0
Cannot find device "wg0"
[warn] WireGuard interface failed to come 'up', exit code is '1'
```

**A23.** This indicates that you are not running the container with 'Privileged' mode enabled and/or sysctl for src_valid_mark (both of which are requirements for wireguard).

For unRAID users this can be done by toggling 'Privileged:' to 'on' for the container, and adding the following line to 'Extra Parameters:'

```
--sysctl="net.ipv4.conf.all.src_valid_mark=1"
```

for people using a Docker run command you would add the following lines:-

```
--sysctl="net.ipv4.conf.all.src_valid_mark=1" \
--privileged=true \
```
