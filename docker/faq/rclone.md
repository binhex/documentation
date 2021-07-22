# **Rclone Docker FAQ**

**Q1.** I am seeing the following message in ```/config/supervisord.log```, what does it mean and how can i fix it?:-

```[warn] RCLONE_CONFIG_PATH '<path defined by user>' does not exist, please run 'rclone config --config /config/rclone/config/config.conf' from within the container```

**A1.** The message above means that you have not run through the initial configuration wizard built into Rclone. This has to be done through the CLI and may require a Web Browser to authenticate (depends on Cloud Provider selected).

To run the Rclone wizard open unRAID Web UI and go to Docker tab, then left click the Rclone icon and select 'Console'. Once at the console of the container please issue the following command to start the Rclone wizard:-

```rclone config --config /config/rclone/config/config.conf```

Depending on the Cloud provider you want to use you will now follow different procedures, a few of the popular ones are linked below:-

OneDrive		https://rclone.org/onedrive/<br>
Google Drive	https://rclone.org/drive/<br>
Amazon Drive	https://rclone.org/amazonclouddrive/<br>

*Note* Certain Cloud Providers will require a Web Browser to be present to be able to complete the Rclone configuration, there is an alternative to this via the Rclone auth configuration option - see here:- https://rclone.org/remote_setup/

**Q2.** I have post checking turned on (via env var ```RCLONE_POST_CHECK```) and when looking at the report (located in ```/config/rclone/reports/```) i see the following message, why can Rclone not check the hashes for the remote files?

```xxxxxx hashes could not be checked```

**A2.** This is due to the fact that a cipher is being used to encrypt the data being uploaded to the Cloud provider, however this does **NOT** mean no checks are done, from the Rclone documentation this is explained:-

```Hashes are not stored for crypt. However the data integrity is protected by an extremely strong crypto authenticator.```

**Q3.** I am seeing the following message in ```/config/supervisord.log```, what does it mean and how can i fix it?:-

```[crit] RCLONE_MEDIA_SHARES not defined (via -e RCLONE_MEDIA_SHARES), exiting script...```

**A3.** The message above means that you have not defined via the env var ```RCLONE_MEDIA_SHARES``` what you want to copy/sync to your cloud provider. In order to resolve this you need to edit the ```Value``` for env var ```RCLONE_MEDIA_SHARES``` and put in thh you want to copy/sync.

For example, so assuming you set container path ```/media``` to host path ```/mnt/user``` and you have a share name of ```Movies```, you would set the ```value``` for ```RCLONE_MEDIA_SHARE``` to ```/media/Movies```, that would then do a recursive copy/sync of all files and folders in the share ```Movies```.

If you want only part of the share copied/sync'd then specify ```Value``` for ```RCLONE_MEDIA_SHARE``` as a sub directory of the share, e.g. ```/media/Movies/SD```. If you want to specify multiple paths then please separate the paths with a comma, e.g.:- ```/media/Movies/SD,/media/Movies/HD```

**Note** When specifying media paths please ensure you take care to match the case of the path, remember Linux IS case sensitive.