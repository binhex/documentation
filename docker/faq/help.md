# **Further Help**

OK so you've read any relevant FAQ's from [here](https://github.com/binhex/documentation) and you're still stuck, in that case you will need to perform the following, remember without logs there is little chance of resolving your issue:-

1. Delete any existing log file located at /config/supervisord.log, where /config is the host path.
2. Stop the container and delete it (or if you're an unRAID user skip this step - done automatically on change to container config).
3. Set the env var key "DEBUG" to a value of "true".
4. Start the container and wait 5 mins for it to completely finish initialising.
5. unRAID users - Attach the log file located at /config/supervisord.log to the correct forum support thread for the container you are running.  
   Other users - Please paste into pastebin (or similar) and create Github 'Issue' and link to the log file.

**IMPORTANT** - Make sure to remove any reference to your username and password from the supervisord.log **BEFORE** attaching/pasting it.
