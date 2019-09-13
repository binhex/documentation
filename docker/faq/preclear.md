Firstly if you are not comfortable with the command line then i would encourage you to use the excellent Preclear Plugin, link here:- 
https://forums.unraid.net/topic/54648-preclear-plugin/

The Preclear script was created by [Joe L.](https://forums.unraid.net/topic/2732-preclear_disksh-a-new-utility-to-burn-in-and-pre-clear-disks-for-quick-add/) and later modified by [bjp999](https://forums.unraid.net/topic/30921-unofficial-faster-preclear/), all credit goes to both of these authors for the script.

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

**Q4.** Can i perform other partial wipes or testing of the disk with this script?

**A4.** Yes there are multiple options with the preclear script, please execute the following to view all options:-

```preclear_binhex.sh -h``` 

**Q5.** Can i perform multiple disk preclears at the same time?.

**A5.** Yes this is possible, you simply create an additional 'tab' in the 'Xfce terminal' and then run the script again against the additional named drive.

**Q6.** I have an enhancement to the preclear script, can i get it included in the next Docker image build?

**A6.** The function of this Docker image is to produce an easy, repeatable way of running the preclear script with minimal hassle, additional features will not be considered at this time, bug fixes only.
