# **Crafty Controller Docker FAQ**

**Q1.** How do i define the version of Java used when running a Minecraft server via Crafty?

**A1.** In order to specify the version of Java you need to edit an already defined server, you cannot define this at the time of server creation in Crafty (this may change in later versions). The procedure for changing the version of Java after the server is defined is as follows:-

1. Open Crafty Web UI and login
2. Click on the 'Dashboard'
3. Click on 'Edit' for running server
4. Scroll down to 'Java Path' and set the path to one of the versions of Java shown below:-

**Java 8 (default)**
```
/usr/lib/jvm/java-8-openjdk/bin/java
```
**Java 11 (required by papermc)**
```
/usr/lib/jvm/java-11-openjdk/bin/java
```
**Java 16 (required by minecraft 1.17+)**
```
/usr/lib/jvm/java-16-openjdk/bin/java
```
5. Click on 'Save'
6. Restart the server for the change to take affect