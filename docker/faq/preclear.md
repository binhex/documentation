# **Preclear Docker FAQ**

Firstly if you are **not** comfortable with the command line then i would encourage you to use the excellent 'Preclear Plugin' instead of this, link here:- 
https://forums.unraid.net/topic/54648-preclear-plugin/

The Preclear script was created by [Joe L.](https://forums.unraid.net/topic/2732-preclear_disksh-a-new-utility-to-burn-in-and-pre-clear-disks-for-quick-add/) and later modified by [bjp999](https://forums.unraid.net/topic/30921-unofficial-faster-preclear/), all credit goes to both of these authors for the script.
___
**IMPORTANT**:- Please do **NOT** perform a backup of the running container (using something like 'CA Appdata Backup/Restore v2'), as this could 'pause' (or stop) the container during the backup process which can/may cause issues for the preclear script on 'resume' or restart (not tested).

**IMPORTANT**:- If you have 'CA Auto Update' plugin installed then please ensure that the Preclear Docker container is **NOT** set to automatically update, as an update to the docker container will cause the preclear script to terminate. In order to exclude this container from auto updates you need to go to unRAID Web UI/Plugins/CA Auto Update Applications/Docker Auto Update Settings tab, select 'No' from the dropdown for 'Update All Docker Applications' then re-select all the containers you want to auto update **EXCLUDING** the Preclear docker container, then click on 'Apply' to save.

**IMPORTANT**:- Please also ensure that you haven't set the server to go into a sleep/hibernate state whilst a preclear is running, as this also can/may cause the preclear script to abort abnormally and require a re-start from the beginning.
___
**Q1.** What is the purpose of the preclear script, and is it still required?

**A1.** Originally the preclear script was designed to do one thing, preclear drives in readiness to be added to the array, the reason we wanted to preclear the drives up front is because doing this using unraid used to cause the entire array to be inaccessible until the preclear had finished, which can take many hours.

The script then got enhanced to do other stuff as well, such as stress testing of the drive using various linux utils, rolling forward in time and preclearing a drive via unraid is now done in the background, allowing the array to carry on working and thus no down time, meaning no requirement to preclear up front.

However the need to stress test a drive is still present due to the bathtub curve (https://www.backblaze.com/blog/how-long-do-disk-drives-last/) and thus the preclear script lives on as a stress test tool.

**Q2.** Why has this docker image been created, as we already have a unraid preclear plugin?.

**A2.** Due to the fact that plugins rely on the underlying OS (Slackware) in order to run, any changes made by Limetech can potentially lead to a breakage with the preclear plgin, this has historically happened a number of times and is unfortunately a fact of life. So how do we try and mitigate this from happening?, by using Docker, this then gives us a known platform from which to run the preclear script, and should reduce the chances of this happening.

**Q3.** How do i preclear a disk?

**A3.** Left click the preclear icon in the Docker tab in the unraid web ui and then choose 'WebUI', then if a terminal window is not showing then right click the desktop and select 'Xfce terminal', then run the following command in the window to list drives that can be precleared:-

```preclear_binhex.sh -l```

This will then list drives which are candidates for preclering, make a note of the 'sdX' name of the drive you want to preclear and then issue the following command, where XXX is the name of the drive from the previous command:-

```preclear_binhex.sh -f /dev/XXX```

**Note**:- The -f flag performs the 'faster' preclear, as enhanced by forum member 'bjp999' and is optional.

**IMPORTANT**:- Please double check the serial number of the drive shown in the confirmation screen **BEFORE** you type 'Yes', if you preclear a drive with data on it accidentally it will be impossible to recover the data off the drive.

**Q4.** Can i perform other partial wipes or test to ensure the drive is precleared with this script?

**A4.** Yes there are multiple options with the preclear script, please execute the following to view all options:-

```preclear_binhex.sh -h``` 

**Q5.** Can i preclear multiple disks at the same time?.

**A5.** Yes this is possible, you simply create an additional 'tab' in the 'Xfce terminal' and then run the script again against the additional named drive.

**Q6.** Can i preclear a drive connected via USB?.

**A6.** Yes this is possible, you simply connect the drive to the USB port and once the drive is detected by the OS then you should be able to preclear as per normal.

**Note**:- You do **NOT** need to install the 'unassigned devices' plugin for the above to work.

**Q7.** Can i safely close the noVNC Web UI window whilst a preclear is running?.

**A7.** Yes this is perfectly safe to do, as long as the terminal window running preclear is **NOT** closed then the preclear script will continue as a background process of the running container. If you then later on wish to check on progress of the preclear script then you simply left click the preclear icon and select 'WebUI' to get back to the terminal window.

**Q8.** The preclear script has identified a drive as a candidate for clearing, but when i attempt to run a preclear i am seeing the following message, what is the cause of this?

```Sorry: /dev/sdX does not exist as a block device```

**A8.** This is due to the fact that the preclear docker container was started before you plugged in your drive. In order to fix this simply restart the container and try the command again.

**Q9.** I have a Seagate hard drive and i am seeing unusually high SMART Raw Values for 'Raw_Read_Error_Rate', 'Seek_Error_Rate' and 'Hardware_ECC_Recovered', is this something to be concerned about?.

**A9.** High 'Raw Values' for those Attributes with Seagate drives is perfectly normal and not usually something to be concerned about. The Raw Values are in fact a count of the actual sectors read, the errors are in the upper 16bits of the 48bit word.

**Q10.** I have an enhancement to the preclear script, can i get it included in the next Docker image build?

**A10.** The function of this Docker image is to produce an easy, repeatable way of running the preclear script with minimal hassle, additional features will not be considered at this time, bug fixes only.
___
**Notes**

**Alignment Options**

Below is the logic built into the preclear script when determining the starting sector for the partition:-
- if preclear -a option chosen and drive < 2.2TB then will set to 63.
- if preclear -a option chosen and drive > 2.2TB then will set to 64.
- if preclear -A option chosen and drive < 2.2TB then will set to 64.
- if preclear -A option chosen and drive > 2.2TB then will set to 64.
- if preclear -a or -A not defined then 63 or 64 will be chosen based on unraid config, **UNLESS** the drive is > 2.2TB, in which case it will always be 64.