# **Further Help**

OK, so you've read any relevant FAQs from [here](https://github.com/binhex/documentation) and you're still stuck. In that case, you will need to perform the following. Please keep in mind that without logs I have no detailed visibility of your configuration and thus little chance of identifying the issue.

## Unraid Users

Please perform both sections below unless instructed otherwise:

### Gather Command Execution

1. Left-click the container and select 'Edit', make any change to a value, then switch it back to what it was and click on 'Apply'. Then copy and paste the 'Command execution' shown to a file, ensuring to **remove all references to username and password**.
2. Go to the support thread (left-click icon and select 'Support') and then attach (do NOT paste) the 'Command execution' output file, but do not 'Submit Reply'. Please move on to the next step below.

### Gather Supervisor Log

1. Delete any existing log file located at `/config/supervisord.log`, where `/config` is the host path for the container.
2. Left-click the container and select 'Edit', then set the 'Container Variable' for `DEBUG` to a value of `true` and click 'Apply'.
3. Wait 5 minutes for it to completely finish initializing, then open the log file at `/config/supervisord.log` and **remove all references to username and password from the file**, then save the log somewhere.
4. Attach (do NOT paste) the log output file from Step 3 to the post and click on 'Submit Reply'.

## Other Users

1. Delete any existing log file located at `/config/supervisord.log`, where /config is the host path.
2. Stop and delete the container (not the image).
3. Set the env var key `DEBUG` to a value of `true`.
4. Create the container and wait 5 minutes for it to completely finish initializing.
5. Open the log file at `/config/supervisord.log` and **remove all references to username and password** and save to another filename.
6. Append the docker run/create command or docker compose yml file to the saved log file.
7. Paste the contents of the saved log file to Pastebin (or similar) and create a GitHub 'Issue' and link to the log file detailing your issue.
