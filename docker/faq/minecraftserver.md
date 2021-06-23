# **Minecraft Server Docker FAQ**

**Q1.** How do i attach to the Minecraft Server console when using this Docker container?

**A1.** Minecraft Server (Java edition) uses a program called 'screen' in order to allow you to connect and disconnect from the running console. In order to connect to the console once the container is running you would issue the following command from your hosts shell:-

```
docker exec -u nobody -it <name of container> /usr/bin/minecraftd console
```

You will then be able to execute 'help' in order to see available commands.

In order to disconnect from the running Minecraft console without stopping the server, you press:-

```
CTRL + a then release and press d
```

Use command 'clear' to clear the screen of any previous output, you can then re-connect to the session again if required using the command above.

**Note** If you do press CTRL + c by accident then you will need to restart the container in order to be able to connect to the console again, as this will kill the running Minecraft Server.

**Q2.** How do i define the version of Java used when running a Minecraft server?

**A2.** This is done by changing the value for env var ```JAVA_VERSION```, valid values are ```8```,```12```, or ```16```, the procedure for this is as follows:-

1. Launch unRAID Web UI and go to 'Docker' tab.
2. Left click the Minecraft Sever icon and select 'Edit'
3. Scroll down until you see ```Container Variable: JAVA_VERSION``` then click on ```Edit```
4. Set the value to the version of Java you want. e.g:-

Value set to ```8```	= Java 8 (Default Java version)<br/>
Value set to ```11```	= Java 11 (Required by papermc)<br/>
Value set to ```16```	= Java 16 (Required by Minecraft 1.17+)<br/>

5. Click on ```SAVE```
6. Click on ```Apply```