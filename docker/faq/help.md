# **Further Help**

OK so you've read all the FAQ's above right and you're still stuck, in that case you will need to perform the following, remember without logs there is little chance of resolving your issue:-

1. Delete any existing supervisord.log file located in host path for /config
2. Stop the container and delete it (or if you're an unRAID user skip this step).
3. Set the env var key "DEBUG" to a value of "true".
4. Start the container and wait 5 mins for it to completely finish initialising.
5. Post the ENTIRE file in the correct support thread for the container you're running, log file located at:-

```/config/supervisord.log```

**IMPORTANT** - Make sure to remove any reference to your username and password from the supervisord.log **BEFORE** posting it.