# **Code-Server Docker FAQ**

**Q1.** I'm seeing an error in the Web UI that states ```Error loading webview: Error: Could not register service workers: S...```, what is it and how can i stop it appearing?

**A1.** This error is caused by the use of self-signed certificates, which by default this container uses. The error can be triggered by simply going to an extesion and attempting to view the 'Details' tab.

To Fix this issue you need to switch to a valid certificate signed by a trusted Certifiate Authority (CA), once you have the certificate you then use the following procedure to confoigure code-server to use the cert:-

1. Stop the container
2. Copy the certificate and key to ```/config/code-server/certs/```
3. Set the value for variable key ```CERT_PATH``` to the path for the cert and set the value for variable key ```CERT_KEY_PATH``` to the path for the cert key.
4. Start container

**Q2.** I want to use a reverse proxy such as SWAG or NPM with Code-Server so i can access it externally, in order to do this i need to switch over to HTTP, how do i do this?.

**A2.** To do this simple set variable key ```SELF_SIGNED_CERT``` to have a value of ```no```, also ensure variable keys ```CERT_PATH``` and ```CERT_KEY_PATH``` both have blank values, otherwise they will take presedence over ```SELF_SIGNED_CERT```.

**Q3.** I would like to include application/tool/scripting language XXXX in Code-Server, how do i do this?.

**A3.** At the present time i do not support the inclusion of additional user defined tooling into Code-Server, if there is a popular tool that the community wants then i would consider adding this in, but for now no custom tooling permitted from the end user.

**Q4.** I want to use the GitHub integration with Code-Server but i don't know what the steps are, can you please tell me how to do this?.

**A4.** To do this simple set variable key ```BIND_CLOUD_NAME``` to have a value of ```<name you want>```, then check the log file ```/config/supervisord/log``` for URL to authorise CDR (cloud provider) with GitHub.