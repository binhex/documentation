# **Further Help**

OK so you've read any relevant FAQ's from [here](https://github.com/binhex/documentation) and you're still stuck, in that case you will need to perform the following, remember without logs there is little chance of resolving your issue:-

## UNRAID Users

Please perform both sections below unless instructed otherwise:

### Gather Command execution

1. Left click the container and select 'Edit', make any change to a value, then switch it back to what it was and click on 'Apply', then copy and paste the 'Command execution' shown to a file, ensuring to **remove all reference to username and password**.
2. Go to support thread (left click icon and select 'Support') and then attach (do NOT paste) the 'Command execution' output file.

### Gather Supervisor log

1. Delete any existing log file located at `/config/supervisord.log`, where `/config` is the host path for the container.
1. Left click container and select 'Edit' then set the 'Container Variable' for `DEBUG` to a value of `true` and click 'Apply'.
1. Wait 5 mins for it to completely finish initialising, then open the log file at `/config/supervisord.log` and **remove all reference to username and password from the file**, then save the log somewhere.
1. Go to the support thread (left click icon and select 'Support') and then attach (do NOT paste) the log output file from Step 3.

## Other Users

1. Delete any existing log file located at `/config/supervisord.log`, where /config is the host path.
1. Stop and delete the container (not the image).
1. Set the env var key `DEBUG` to a value of `true`.
1. Create the container and wait 5 mins for it to completely finish initialising.
1. Open the log file at `/config/supervisord.log` and **remove all reference to username and password** and save to another filename.
1. Append the docker run/create command or docker compose yml file to the saved log file.
1. Paste the contents of the saved log file to pastebin (or similar) and create Github 'Issue' and link to the log file detailing your issue.
