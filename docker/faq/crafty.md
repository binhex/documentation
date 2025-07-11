# **Crafty Controller Docker FAQ**

**Q1.** How do I define the version of Java used when running a Minecraft server via Crafty?

**A1.** In order to specify the version of Java, you need to edit an already defined server; you cannot define this at the time of server creation in Crafty (this may change in later versions). The procedure for changing the version of Java after the server is defined is as follows:

1. Open the Crafty Web UI and log in.
2. Click on the 'Dashboard'.
3. Click on 'Edit' for the running server.
4. Scroll down to 'Java Path' and set the path to one of the versions of Java shown below:

**Java 8 (default)**
```
/usr/lib/jvm/java-8-openjdk/bin/java
```
<br/>

**Java 11 (required by PaperMC)**
```
/usr/lib/jvm/java-11-openjdk/bin/java
```
<br/>

**Java XX (latest) (required by Minecraft 1.17+)**<br/>
Due to the rolling nature of Arch Linux and the fact Java versions are changing quickly nowadays, I cannot detail the exact path to use. You should be able to identify the latest version included in the image by going to the following link: https://archlinux.org/packages/extra/x86_64/jdk-openjdk/<br/>

Once you know the major version (e.g., `18`), then simply replace `XX` with the version number to get the path to the latest Java included in the image.
```
/usr/lib/jvm/java-XX-openjdk/bin/java
```
5. Click on 'Save'.
6. Restart the server for the change to take effect