# **Newbie VPN Guide**

**Q1.** I can't get the Web UI to show for application xVPN when VPN_ENABLED is set to 'yes', if i set it to 'no' then i can view the Web UI, what am i doing wrong?

**A1.** Firstly in order to create a tunnel to your VPN provider you need to use their OpenVPN configuration file and certificates. These will typically be downloaded from your VPN providers website, and generally are zipped. 

**PIA users** - The URL to download the openvpn configuration files and certs is https://www.privateinternetaccess.com/openvpn/openvpn.zip

Once you have downloaded the zip (normally a zip as they contain multiple ovpn files) then extract it to /config/openvpn/ folder (if that folder doesn't exist then start and stop the docker container to force the creation). If there are multiple ovpn files then please delete the ones you don't want to use (normally filename follows location of the endpoint) leaving just a single ovpn file and the referenced certificates (normally files with a crt and pem extension).

You will now need to move onto configuration of the container...

So all of the Docker images i have produced that include VPN functionality will NOT allow you to access the Web UI of the application until there is a working VPN tunnel. This protects the user from accidentally thinking they have a working tunnel and thus are anonymous, when in actual fact they aren't protected at all.

Whilst a lot of VPN providers are subtly different, i will try to give some examples for each setting exposed via Environment Variables (via 'docker run' or unRAID Web UI):-

**VPN_ENABLED** - Fairly self explanatory, if you set this to 'yes' then a VPN tunnel connection will be attempted, set to no and you will run the application with no VPN protection.

**VPN_USER** - This is the username as supplied by your VPN provider, it might be the website login, or it might be a separate username. In some cases it may also not be required, as some providers allow you to create a ovpn config file with your authentication supplied as a inline end user certificate (AirVPN for instance).

**VPN_PASS** - Same deal as the VPN_USER, this is the VPN provider supplied password, again this may not be necessary for certain providers due to auth via embeded cert in the ovpn file.

**IMPORTANT** - usernames/passwords that contain characters which are NOT in the range (0-9, a-z, A-Z) MAY cause issues, check the /config/supervisord.log for this

**VPN_PROV** - This is the VPN provider you're using, the reason i differentiate between providers is because i have built in support for port forwarding for propvider PIA, thus its important to specify this correctly, if you aren't using VPN provider PIA then set it to either AirVPN or custom.

**VPN_OPTIONS** - This allows you to define advanced OpenVPN options, in most cases this is NOT required unless you know what you're doing.

**STRICT_PORT_FORWARD** - If this is set to yes then you will be enforcing port forwarding when connected to an VPN remote endpoinly for provider PIA. Again i would like to stress this only takes effect for PIA users only, if you're using another provider then you will need to setup the port forward yourself (speeds will be VERY slow without a working incoming port).

**ENABLE_PRIVOXY** - Allows you to define whether you want to run Privoxy inside the container as well - for more details about Privoxy see below Q4.

https://forums.lime-technology.com/topic/44108-support-binhex-general/?do=findComment&comment=433613

**LAN_NETWORK** - This is used to define your home LAN network, do NOT confuse this with the IP address of your router or your server, the value for this key defines your network NOT a single host - for more details about how to configure this see below Q5.
https://forums.lime-technology.com/topic/44108-support-binhex-general/?do=findComment&comment=433613

**NAME_SERVERS** - This allows you to define the name servers you want to use when the VPN tunnel is established, keep in mind you probably will NOT be able to use your ISP's name servers when the tunnel is running, as your IP address will then not be in your ISP's range and thus will normally be blocked, thus the recommendation to use an open DNS, the defaults are normally fine.

**DEBUG** - Set this to true to enable debug, extremely useful to debug issues when you can't connect to the VPN tunnel - for further help see below

https://forums.lime-technology.com/topic/44108-support-binhex-general/?do=findComment&comment=435831

**UMASK** - This sets the permissions for newly created files/folders, the defaults are normally fine.

**PUID/PGID** - The user ID and group ID to run as, the default value of 99 is for user 'nobody' group 'users', if you want to run the container as another user then find out the UID by issuing the following command:-

```id <username you want to run as>```

Example command run on the docker host:-

```
# id nobody
# uid=99(nobody) gid=100(users) groups=100(users),98(nobody)
```

So you can see the UID is 99 and the GID is 100, so set PUID to be 99 and PGID to be 100.

**IMPORTANT** - If you do decide to change the PUID and PGID values and you have previously started the container then please ensure you delete the file ```/config/perms.txt``` to force a reset of permissions for the new user/group.