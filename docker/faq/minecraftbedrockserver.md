# **Minecraft Bedrock Server Docker FAQ**

**Q1.** How do i attach to the Minecraft Bedrock Server console when using this Docker container?

**A1.** Minecraft Bedrock Server uses a program called 'screen' in order to allow you to connect and disconnect from the running console. In order to connect to the console once the container is running you would issue the following command from your hosts shell:-

```
docker exec -u nobody -it <name of container> screen -r minecraft
```

You will then be able to execute 'help' in order to see available commands.

In order to disconnect from the running Minecraft console without stopping the server, you press:-

```
CTRL + a + d
```

Use command 'clear' to clear the screen of any previous output, you can then re-connect to the session again if required using the command above.

**Note** If you do press CTRL + c by accident then you will need to restart the container in order to be able to connect to the console again, as this will kill the running Minecraft Server.

Reddit post 'Bedrock Dedicated Server Tutorial':-
https://www.reddit.com/user/ProfessorValko/comments/9f438p/bedrock_dedicated_server_tutorial/?utm_source=share&utm_medium=web2x