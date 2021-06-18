# **Code-Server Docker FAQ**

**Q1.** I'm seeing an error in the Web UI that states ```Error loading webview: Error: Could not register service workers: S...```, what is it and how can i stop it appearing?

**A1.** This error is caused by the use of self-signed certificates, which by default this container uses. The error can be triggered by simply going to an extesion and attempting to view the 'Details' tab.

To Fix this issue you need to switch to a valid certificate signed by a trusted Certifiate Authority (CA), once you have the certificate you then use the following procedure to confoigure code-server to use the cert:-

TBD

**Q2.** I would like to include application/tool/scripting language X in Code-Server, how do i do this?.

**A2.** At the present time i do not support the inclusion of additional tooling into Code-Server, if there is a popular tool that the community wants then i would consider adding this in, but for now no custom tooling permitted from the end user.

**Q3.** I want to use the GitHub integration with Code-Server but i don't know what the steps are, can you please tell me how to do this?.

**A3.** TBD