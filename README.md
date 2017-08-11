# streaming-radio
This is primarily an instructional repo that outlines the steps to use IceCast and Gqrx on DragonBoards 820c in creating a streaming SDR.

# Initial Setup
This demo uses an RTL-SDR USB audio dongle based on the RTL2832U chipset. The good news is these are only $20! The Gqrx open source application connects to the dongle and decodes full-spectrum radio including FSRS, Amature Radio bands, FM Radio, National Weather frequencies, Air Traffic control, and more.   It then leverages the streaming UDP output of Gqrx, uses VLC to convert this to Ogg and sends it to the open source streaming audio server application IceCast, creating a streaming radio that streams the radio frequencies being played to client computers.   The high level demo setup is shown in the picture below.

![alt text](Images/Qualcomm-DragonBoard-820cStreamingRadioDemoSetupDemo.jpg "Demo Setup")

Build 68 of the DragonBoard 820c was used and can be found on the 96boards web site.  A later build could be used and new features are being added regularly, so a later build may be a good choice. The builds can be found [here](http://builds.96boards.org/snapshots/dragonboard820c/linaro/debian/).

The remainder of this section goes through the steps to configure the DragonBoard 820c for the demo.

## Install Debian image on the Dragonboard 820c 
At this time, the DragonBoard 820c is in pre-release and only very limited availability.  The instructions are also not released publicly.  If you have access to internal linaro wiki, the instructions to verify firmware version, update the firmware, and install a developer version of Debian can be found [here](https://wiki.linaro.org/Internal/QCOM/DragonBoard-820c).  

# References
* [The Gqrx SDR home page](http://gqrx.dk/)


# Extra Credit 
If you got this far, congratulations!  You are streaming radio over your home network, and with minor modifications, even over the internet!  This section lists some possible follow-on extensions to the project that could be fun.
* Build and use a gstreamer pipeline instead of VLC to convert the output of Gqrx into an IceCast source stream. I've given it a try, but unsuccessful to date.  Ping me if you want my gstreamer pipelines I have attempted to no avail.
* Use other SDR's insteam of Gqrx.
* Build Gqrx from source and redo the demo.  This would be the first step in investigating ways to do some performance improvements.
* Use more elaborate and advances antennas to improve reception.
* Create a scanner.  Some starting code can be found for this in the community.
* Punch through your firewall and stream to the internet.
* Build your DragonBoard 820c from source.  Also could build and Open Embedded (OE) image and repeat the demo.
* others?
