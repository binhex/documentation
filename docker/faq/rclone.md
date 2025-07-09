# **Rclone Docker FAQ**

**Q1.** I am seeing the following message in `/config/supervisord.log`. What does it mean, and how can I fix it?

```
[warn] RCLONE_CONFIG_PATH '<path defined by user>' does not exist, please run 'rclone config --config /config/rclone/config/rclone.conf' from within the container
```

**A1.** The message above means that you have not run through the initial configuration wizard built into Rclone. This has to be done through the CLI and may require a web browser to authenticate (depends on the cloud provider selected).

To run the Rclone wizard, open the Unraid web UI and go to the Docker tab, then left-click the Rclone icon and select 'Console'. Once at the console of the container, please issue the following command to start the Rclone wizard:

```
rclone config --config /config/rclone/config/rclone.conf
```

Depending on the cloud provider you want to use, you will now follow different procedures. A few of the popular ones are linked below:

OneDrive        https://rclone.org/onedrive/<br>
Google Drive    https://rclone.org/drive/<br>
Amazon Drive    https://rclone.org/amazonclouddrive/<br>

*Note*: Certain cloud providers will require a web browser to be present to complete the Rclone configuration. There is an alternative to this via the Rclone auth configuration option—see here: https://rclone.org/remote_setup/

**Q2.** I have post-checking turned on (via env var `RCLONE_POST_CHECK`), and when looking at the report (located in `/config/rclone/reports/`) I see the following message. Why can Rclone not check the hashes for the remote files?

```
xxxxxx hashes could not be checked
```

**A2.** This is due to the fact that a cipher is being used to encrypt the data being uploaded to the cloud provider. However, this does **NOT** mean no checks are done. From the Rclone documentation, this is explained:

```
Hashes are not stored for crypt. However, the data integrity is protected by an extremely strong crypto authenticator.
```

**Q3.** I am seeing the following message in `/config/supervisord.log`. What does it mean, and how can I fix it?

```
[crit] RCLONE_MEDIA_SHARES not defined (via -e RCLONE_MEDIA_SHARES), exiting script...
```

**A3.** The message above means that you have not defined via the env var `RCLONE_MEDIA_SHARES` what you want to copy/sync to your cloud provider. In order to resolve this, you need to edit the `Value` for the env var `RCLONE_MEDIA_SHARES` and put in what you want to copy/sync.

For example, assuming you set container path `/media` to host path `/mnt/user` and you have a share named `Movies`, you would set the `value` for `RCLONE_MEDIA_SHARES` to `/media/Movies`. That would then do a recursive copy/sync of all files and folders in the share `Movies`.

If you want only part of the share copied/synced, then specify the `Value` for `RCLONE_MEDIA_SHARES` as a subdirectory of the share, e.g., `/media/Movies/SD`. If you want to specify multiple paths, then please separate the paths with a comma, e.g.: `/media/Movies/SD,/media/Movies/HD`

**Note**: When specifying media paths, please ensure you take care to match the case of the path. Remember, Linux IS case sensitive.