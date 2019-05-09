# **SABnzbdVPN Docker FAQ**

**Q1.** I am having issues using a post processing script which connects to CouchPotato/Sick Beard, how do i fix this?

**A1.** In order to allow SABnzbdVPN to initiate a connection to an application you need to define the additional ports required by configuring the "ADDITIONAL_PORTS" environment variable with the port(s) you need to open, if you need more than one port opening then please use a comma to seperate the values. To be clear this parameter is NOT required for normal use, this is only required when specifying scripts which connect FROM SABnzbd to another container/application.

**WARNING** misconfiguration of this COULD lead to ip leakage, do NOT configure this with the port for your usenet provider!.

Please note, this is a new environment variable that has been added later on, thus existing users may not have this present for the docker configuration screen, if this is the case you will need to manually add in the environment variable.

**Q2.** How can i verify that the Usenet client is using the VPN tunnel?

**A2.** You can verify this by downloading a public webpage inside the running Docker container which returns your public ip address, instructions on how to do this shown below:-

1. SSH into your host

2. Run the following command:-

&nbsp;&nbsp; ```docker exec -it <name of container> curl -L https://jsonip.com```

3. The returned IP value should be different to your ISP assigned IP address

**Q3.** I've just updated to the latest SABnzbd image and now i can't access the web ui, if i look in the /config/supervisord.log file i see the following, how can i fix it?

```
Traceback (most recent call last):
  File "/opt/sabnzbd/cherrypy/wsgiserver/__init__.py", line 2024, in start
    self.tick()
  File "/opt/sabnzbd/cherrypy/wsgiserver/__init__.py", line 2091, in tick
    s, ssl_env = self.ssl_adapter.wrap(s)
  File "/opt/sabnzbd/cherrypy/wsgiserver/ssl_builtin.py", line 67, in wrap
    server_side=True)
  File "/usr/lib/python2.7/ssl.py", line 363, in wrap_socket
    _context=self)
  File "/usr/lib/python2.7/ssl.py", line 611, in __init__
    self.do_handshake()
  File "/usr/lib/python2.7/ssl.py", line 840, in do_handshake
    self._sslobj.do_handshake()
error: [Errno 0] Error
```

**A3.** The above error indicates that the currently used certificate and key are too weak and thus must be replaced with a stronger cert/key. If your using the self-signed certificate that comes with SABnzbd then you can simply stop the Docker container, and then delete the files /config/admin/server.key and /config/admin/server.cert then start the container again to force the regeneration of compliant certs.

**Note** - There is currently a bug in SABnzbd where it still shows an error message on the web ui, this is completely harmless and can be dismissed, if you wish to help get this bug removed then please post about it here:- https://github.com/sabnzbd/sabnzbd/issues/853
