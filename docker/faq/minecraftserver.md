# **Minecraft Server Docker FAQ**

**Q1.** How do I attach to the Minecraft Server console when using this Docker container?

**A1.** Minecraft Server (Java Edition) uses a program called 'screen' in order to allow you to connect and disconnect from the running console. In order to connect to the console once the container is running, you would issue the following command from your host's shell:

```
docker exec -u nobody -it <name of container> /usr/bin/minecraftd console
```

You will then be able to execute 'help' in order to see available commands.

In order to disconnect from the running Minecraft console without stopping the server, press:

```
CTRL + a, then release and press d
```

Use the command 'clear' to clear the screen of any previous output. You can then reconnect to the session again if required using the command above.

**Note**: If you do press CTRL + c by accident, then you will need to restart the container in order to be able to connect to the console again, as this will kill the running Minecraft Server.

**Q2.** How do I define the version of Java used when running a Minecraft server?

**A2.** This is done by changing the value for the env var `JAVA_VERSION`. Valid values are `8`, `11`, or `16`. The procedure for this is as follows:

1. Launch Unraid Web UI and go to the 'Docker' tab.
2. Left-click the Minecraft Server icon and select 'Edit'.
3. Scroll down until you see `Container Variable: JAVA_VERSION`, then click on `Edit`.
4. Set the value to the version of Java you want. For example:

Value set to `8`   = Java 8 (Default Java version)<br/>
Value set to `11`  = Java 11 (Required by PaperMC)<br/>
Value set to `16`  = Java 16 (Required by Minecraft 1.17+)<br/>

5. Click on `SAVE`.
6. Click on `Apply`.