# **Jellyfin Docker FAQ**

**Q1.** How do I configure Jellyfin to use hardware transcoding (transcoding using GPU instead of CPU)?.

**A1.** In order to get hardware transcoding working for your GPU you need to configure the container as follows:-

**nVidia GPU**

1. Install the unRAID Plugin `Nvidia Driver` from CA (Community Applications), please ensure the driver version you select is at least `470.57.02`.
2. Go to unRAID web ui/Docker tab/left click Jellyfin container and select `Edit`
3. Click on the toggle for `ADVANCED VIEW` (top right)
4. Go to `Extra Parameters:` and enter in a value of `--gpus all`
5. Click on `Add another Path, Port, Variable, Label or Device` and select `Config Type` of `Variable`
6. Set `Key:` to `NVIDIA_VISIBLE_DEVICES` and `Value:` to `all`
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

**Note** To help debug further it is highly recommended to also install the unRAID Plugin `GPU Statistics`, this will then give you stats on your GPU and will enable you to easily see when the GPU is being used for transcoding.