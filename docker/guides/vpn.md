# **VPN Setup Guide**

## **Prerequisites**<br>

**Sign up to VPN provider**<br>
The first task is to sign up with a VPN provider. Not all providers are the same; things to look for in a VPN provider:

1. Privacy - Ensure the VPN provider does NOT log anything! If they do, then walk away.
2. Permitted Traffic - Ensure the VPN provider does allow P2P traffic and doesn't block or throttle.
3. Incoming Ports - Ensure the VPN provider supports the generation of an incoming port for your account (most don't permit this). If they don't, then speeds will be low due to restricted access to peers.

Recommendations are:

- PIA (pre-configured for you, easiest approach)
- Mullvad (great for privacy, highly recommended)
- AirVPN (good, solid VPN provider with incoming port support)

**Download config and generate incoming port**<br>
Once you have settled on a VPN provider that you like, configure an incoming port (not required for PIA users) and download the OpenVPN and/or Wireguard configuration files. These are typically zipped as they contain multiple configuration files for the multiple servers that you can connect to.

**PIA users** - The URL to download the OpenVPN configuration files and certs is <https://www.privateinternetaccess.com/openvpn/openvpn.zip>

IMPORTANT: Please do **NOT** configure your home router for the incoming port, see Q15: <https://github.com/binhex/documentation/blob/master/docker/faq/vpn.md>

**Placement of VPN client config**<br>
Once you have the VPN client configuration file, you need to start and then stop the container—it will auto-stop as there is no configuration present at that time. Then copy a SINGLE OpenVPN config file (normally has extension .ovpn) or Wireguard config file (normally has extension .wg0) to `/config/openvpn/` or `/config/wireguard/` respectively, as well as any bundled certificates.

**Record credentials from VPN provider**<br>
Most VPN providers have a set of credentials that are used to authenticate with OpenVPN and/or Wireguard. If your VPN provider embeds the authentication into the OpenVPN/Wireguard configuration file, then leave `VPN_USER` and `VPN_PASS` credentials blank.

## **Configuration options**<br>

Now, onto configuration of the container. The following is a description of each env var key and value:

**Key Name:** `VPN_ENABLED`<br>
**Description:** Fairly self-explanatory. If you set this to 'yes', then a VPN tunnel connection will be attempted. Set to 'no' and you will run the application with no VPN protection.<br>
**Values:** `yes|no`<br>

**Key Name:** `VPN_USER`<br>
**Description:** This is the username as supplied by your VPN provider. It might be the website login, or it might be a separate username. In some cases, it may also not be required, as some providers allow you to create an ovpn config file with your authentication supplied as an inline end user certificate (AirVPN, for instance).<br>
**Values:** `<vpn username string>`<br>

**Key Name:** `VPN_PASS`<br>
**Description:** Same deal as the VPN_USER. This is the VPN provider-supplied password. Again, this may not be necessary for certain providers due to auth via embedded cert in the ovpn file.<br>
**Values:** `<vpn password string>`<br>

**IMPORTANT** - Usernames/passwords that contain characters which are NOT in the range (0-9, a-z, A-Z) MAY cause issues. Check the `/config/supervisord.log` for this.

**Key Name:** `VPN_PROV`<br>
**Description:** This is the VPN provider you're using. The reason I differentiate between providers is because I have built-in support for port forwarding for provider PIA, thus it's important to specify this correctly. If you aren't using VPN provider PIA, then set it to either AirVPN or custom.<br>
**Values:** `pia|airvpn|custom`<br>

**Key Name:** `VPN_CLIENT`<br>
**Description:**  This defines whether you want to connect to the VPN provider using either the OpenVPN client or the Wireguard client.<br>
**Values:** `openvpn|wireguard`<br>

**Key Name:** `VPN_OPTIONS`<br>
**Description:** This allows you to define advanced OpenVPN options (Wireguard not supported). In most cases, this is NOT required and can cause issues if misconfigured. Please leave undefined unless you know what you are doing.<br>
**Values:** `<openvpn options string>`<br>

**Key Name:** `STRICT_PORT_FORWARD`<br>
**Description:**  **PIA users only**. If this is set to yes, then you will only be able to connect to endpoints that support port forwarding. If you set this to no, then you will be able to connect to ANY PIA endpoint, irrespective of whether it supports port forwarding or not.<br>
**Values:** `yes|no`<br>

**Key Name:** `ENABLE_PRIVOXY`<br>
**Description:** Allows you to define whether you want to run Privoxy inside the container as well. For more details about Privoxy, see Q3: <https://github.com/binhex/documentation/blob/master/docker/faq/vpn.md><br>
**Values:** `yes|no`<br>

**Key Name:** `LAN_NETWORK`<br>
**Description:** This is used to define your home LAN network. Do NOT confuse this with the IP address of your router or your server. The value for this key defines your network (CIDR format), NOT a single host. For help about how to configure this, see Q4: <https://github.com/binhex/documentation/blob/master/docker/faq/vpn.md><br>
**Values:** `<comma separated list of cidr networks>`<br>

**Key Name:** `NAME_SERVERS`<br>
**Description:** This allows you to define the name servers you want to use when the VPN tunnel is established. Keep in mind you probably will NOT be able to use your ISP's name servers when the tunnel is running, as your IP address will then not be in your ISP's range and thus will normally be blocked. Thus, the recommendation to use an open DNS; the defaults are normally fine.<br>
**Values:** `<comma separated list of name servers>`<br>

**Key Name:** `DELUGE_DAEMON_LOG_LEVEL`<br>
**Description:** Deluge daemon logging level.<br>
**Values:** `info|warning|error|none|debug|trace|garbage`<br>

**Key Name:** `DELUGE_WEB_LOG_LEVEL`<br>
**Description:** Deluge web logging level.<br>
**Values:** `info|warning|error|none|debug|trace|garbage`<br>

**Key Name:** `VPN_INPUT_PORTS`<br>
**Description:** This will permit access from the LAN to applications running in the VPN network. For example, if you had a DelugeVPN container running, you could bind the network for Sonarr to the DelugeVPN container so that all traffic for Sonarr is then sent down the VPN tunnel encrypted. In order to then access the Sonarr Web UI from the LAN, you would need to define the Sonarr Web UI port using this key. See Q24 for more details: <https://github.com/binhex/documentation/blob/master/docker/faq/vpn.md><br>
 **Values:** `<comma separated list of input ports>`<br>

IMPORTANT: Please note 'VPN_INPUT_PORTS' is **NOT** to define the incoming port for the VPN. This environment variable is used to define port(s) you want to allow into the VPN network when network binding multiple containers together. Configuring this incorrectly with the VPN provider-assigned incoming port COULD result in IP leakage. You have been warned!

**Key Name:** `VPN_OUTPUT_PORTS`<br>
**Description:** This will permit applications running in the VPN network to access applications on the LAN. An example of this requirement is when having Sonarr/Radarr/Lidarr routed through a VPN container and these apps require access to nzbget running on the LAN. In this case, you would define VPN_OUTPUT_PORTS = 6789 (default port for nzbget). This would then allow the index app (Sonarr/Radarr/Lidarr) to connect to the download client (nzbget). See Q24 for more details: <https://github.com/binhex/documentation/blob/master/docker/faq/vpn.md><br>
 **Values:** `<comma separated list of output ports>`<br>

**Key Name:** `DEBUG`<br>
**Description:** Set this to true to enable debug. Extremely useful to debug issues when you can't connect to the VPN tunnel. For further help, see <https://github.com/binhex/documentation/blob/master/docker/faq/help.md><br>
**Values:** `true|false`<br>

**Key Name:** `ENABLE_STARTUP_SCRIPTS`<br>
**Description:** Set this to yes to enable startup scripts to run from the /config/scripts directory.<br>
**Values:** `yes|no`<br>

**Key Name:** `UMASK`<br>
**Description:** This sets the permissions for newly created files/folders. The default of `000` is normally fine.<br>
**Values:** `<3 digit integer>`<br>

**Key Name:** `PUID`<br>
**Description:** The user ID to run as. The default value of 99 is for user 'nobody'. If you want to run the container as another user, then find out the UID by issuing the following command: `id <user you want to run as>`<br>
**Values:** `<UID integer>`<br>

**Key Name:** `PGID`<br>
**Description:** The group ID to run as. The default value of 100 is for group 'users'. If you want to run the container as another group, then find out the GID by issuing the following command:  `id <group you want to run as>`<br>
**Values:** `<GID integer>`<br>

IMPORTANT: If you do decide to change the PUID and/or PGID values and you have previously started the container, then please ensure you delete the file `/config/perms.txt` to force a reset of permissions for the new user/group.

-------
And lastly, please take a look at the extensive FAQs I have written up:<br>
<https://github.com/binhex/documentation/blob/master/docker/faq/vpn.md>

If you are still stuck, then please do the following:<br>
<https://github.com/binhex/documentation/blob/master/docker/faq/help.md>
