# **Jellyfin Docker FAQ**

**Q1.** How do I configure Jellyfin to use hardware transcoding (transcoding using GPU instead of CPU)?.

**A1.** In order to get hardware transcoding working for your GPU you need to configure the container as follows:-

**nVidia GPU**

1. Install the unRAID Plugin `Nvidia Driver` from CA (Community Applications), please ensure the driver version you select is at **least** `470.57.02`.
2. Go to unRAID web ui/Docker tab/left click Jellyfin container and select `Edit`
3. Click on the toggle for `ADVANCED VIEW` (top right)
4. Go to `Extra Parameters:` and enter in a value of `--gpus all`
5. Click on `Add another Path, Port, Variable, Label or Device` and select `Config Type` of `Variable`
6. Set `Key:` to `NVIDIA_VISIBLE_DEVICES` and `Value:` to `all`, or if you want to pass through a specific card then specify the GPU ID e.g. `GPU-02ac791e-6f42-b14f-3c14-8a4db2a01b8d` (can be found in the `Nvidia Driver` plugin - see step 1.)
7. Click on `Add`
8. Click on `Add another Path, Port, Variable, Label or Device` and select `Config Type` of `Variable`
9. Set `Key:` to `NVIDIA_DRIVER_CAPABILITIES` and `Value:` to `all`
10. Click on `Add`
11. Click on `Apply` to apply the change

Once you have done the above it's time to enable hardware transcoding in Jellyfin:-

1. Login to Jellyfin Web UI and go to the `Administration/Dashboard`
2. Go to `Server/Playback` you should then see the `Transcoding` section
3. On the dropdown for `Hardware acceleration:` select `Nvidia NVENC`
4. Tick the preferred options for `Enable hardware decoding for:`
5. Click `Save` at the bottom of the screen to enable.
6. Test playback and monitor CPU usage

**Note** To help debug further it is highly recommended to also install the unRAID Plugin `GPU Statistics`, this will then give you stats on your GPU and will enable you to easily see when the GPU is being used for transcoding.**

**Q2.** I am attempting to setup Jellyfin from scratch but I'm getting prompted for login credentials, what are the default credentials to login?

**A2.** This is a bug in the Jellyfin Web UI, on initial startup it should be running the wizard to setup credentials for the admin account, if this is not happening and you are instead seeing the login prompt then you need to go to the following URL instead to force the use of the wizard which will walk you through the setup process:- `http://<ip of your host>:8096/web/index.html#!/wizardstart.html`
