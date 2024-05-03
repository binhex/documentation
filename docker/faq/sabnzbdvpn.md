# **PyCharm Docker FAQ**

**Q1.** I am having issues using a post processing script which connects to CouchPotato/Sick Beard, how do i fix this?

**A1.** In order to allow SABnzbdVPN to initiate a connection to an application you need to define the additional ports required by configuring the "ADDITIONAL_PORTS" environment variable with the port(s) you need to open, if you need more than one port opening then please use a comma to seperate the values. To be clear this parameter is NOT required for normal use, this is only required when specifying scripts which connect FROM SABnzbd to another container/application.

