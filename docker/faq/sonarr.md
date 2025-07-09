# **Sonarr Docker FAQ**

**Q1.** I see that there is a v3 of Sonarr available. How do I switch over to this?

**A1.** OK, as Sonarr devs are stubbornly still marking Sonarr v3 as beta (and have been for years!) and to all accounts it's stable enough to be used, I am going to break my golden rule of 'no betas if at all possible' and release this as a tagged version (tag is 'v3').

So if you want to migrate from Sonarr v2 to Sonarr v3, you can now achieve this by doing the following:

1. Go to Unraid web UI > Docker tab, left-click Sonarr and select 'Stop', then left-click again and select 'Edit' and change the 'Repository' from `binhex/arch-sonarr` to `binhex/arch-sonarr:v3`, then click on 'Show more settings...' and note the host path for /config. Copy ALL files and folders from the host path to another host path, noting the new host path you've chosen. For example (run from Unraid Terminal, NOT container console!):
```
mkdir -p /mnt/cache/appdata/binhex-sonarr-v3 && cp -R /mnt/cache/appdata/binhex-sonarr/* /mnt/cache/appdata/binhex-sonarr-v3/
```
2. Now change the path for /config to the new path (in this example `/mnt/cache/appdata/binhex-sonarr-v3`) and finally click on 'Apply' at the bottom.

3. Start Sonarr (if it's not started) and attempt access of the web UI. You should see all your existing TV Series.

4. If you are satisfied that v3 is working as intended and you do not need to roll back to v2, then feel free to delete the old host path for Sonarr (in this example `/mnt/cache/appdata/binhex-sonarr`).

**Note**

If you are using self-generated certificates for your downloaders, then you may need to relax the new default restriction in Sonarr by going to Sonarr's web UI > Settings > General and then the 'Security' heading and setting the dropdown for 'Certificate Validation' to either 'Disabled for Local Addresses' or 'Disabled' and click on 'Save Changes' (top left).

The 'latest' tagged version will still be v2 until v3 is officially released.
