# **Minecraft Bedrock Server Docker FAQ**

**Q1.** How do i attach to the Minecraft Bedrock Server console when using this Docker container?

**A1.** Minecraft Bedrock Server uses a program called 'screen' in order to allow you to connect and disconnect from the running console. In order to connect to the console once the container is running you would issue the following command from your hosts shell:-

```
docker exec -u nobody -it <name of container> screen -r minecraft
```

You will then be able to execute 'help' in order to see available commands.

In order to disconnect from the running Minecraft console without stopping the server, you press:-

```
CTRL + a then release and press d
```

Use command 'clear' to clear the screen of any previous output, you can then re-connect to the session again if required using the command above.

**Note** If you do press CTRL + c by accident then you will need to restart the container in order to be able to connect to the console again, as this will kill the running Minecraft Server.

**Q2.** I'm struggling to work out how you can add a texture pack to the Minecraft Bedrock server, can you please give me a step by step guide on how to do this?

**A2.** Sure!, the steps below were taken from a forum post by member 'PeteAsking':-

1. Stop the Minecraft Bedrock Server container.
2. Download a texture pack, e.g. https://mcpedl.com/fuserealism-resource-pack/ ensuring the filename does not contain spaces, change the extension to .zip
3. Copy the downloaded texture pack to '/config/minecraft/resource_packs/'
4. Also copy the texture pack to '/config/minecraft/worlds/<name of your world>/resource_packs/' - this is so people joining get the texture pack installed on their Minecraft client, otherwise they must have it pre-installed on their device.
5. Rename file '/config/valid_known_packs.json' to a new name.
6. Start and stop the container to auto regenerate the above file with the correct UUID and version values for the texture pack.
7. Open the file '/config/valid_known_packs.json' and make a note of the 'uuid' value - this will be required in a step below.
8. Create a file '/config/minecraft/worlds/<name of your world>/world_resource_packs.json' with the following content, replacing the value of 'pack_id' with the uuid identified in step 7.:-

```
[
    {
        "pack_id" : "<value of uuid in step 7.>",
        "version" : [ 0, 0, 1 ]
    }
]
```
 
9. Open the file 'config/server.properties' and change 'texturepack-required=true' if you want to force people to use the pack. otherwise leave it false to make the texture pack optional.
10. Start the Docker container.
11. The texture pack should now be visible in your Minecraft world.

**Note** If you at any stage get the message 'invalid_resource_pack file' then the pack you downloaded is incompatible with the version of Minecraft Bedrock server that you are running, try another texture pack.

**Tutorial*** Reddit post 'Bedrock Dedicated Server Tutorial':-
https://www.reddit.com/user/ProfessorValko/comments/9f438p/bedrock_dedicated_server_tutorial/?utm_source=share&utm_medium=web2x