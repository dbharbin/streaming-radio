# streaming-radio
This is primarily an instructional repo that outlines the steps to use IceCast and Gqrx on DragonBoards 820c in creating a streaming SDR.

# Initial Setup
This demo uses an RTL-SDR USB audio dongle based on the RTL2832U chipset. The good news is these are only $20! The Gqrx open source application connects to the dongle and decodes full-spectrum radio including FSRS, Amature Radio bands, FM Radio, National Weather frequencies, Air Traffic control, and more.   It then leverages the streaming UDP output of Gqrx, uses VLC to convert this to Ogg and sends it to the open source streaming audio server application IceCast, creating a streaming radio that streams the radio frequencies being played to client computers.   The high level demo setup is shown in the picture below.

![alt text](Images/Qualcomm-DragonBoard820c-StreamingRadioDemo.jpg "Demo Setup")

* Information on the UART Serial Mezzanine can be found [here](https://www.96boards.org/product/uartserial/)
* Information on the DragonBoard 820c can be found **tbd**

Build 68 of the DragonBoard 820c was used and can be found on the 96boards web site.  A later build could be used and new features are being added regularly, so a later build may be a good choice. The builds can be found [here](http://builds.96boards.org/snapshots/dragonboard820c/linaro/debian/).

The remainder of this section goes through the steps to configure the DragonBoard 820c for the demo.

## Install Debian image on the Dragonboard 820c 
To install the Debian image, follow the instructions for **Installing Debian - Console image** found [here](https://github.com/96boards/documentation/wiki/Dragonboard-820c-Getting-Started-With-Linux).  

If you have access to internal linaro wiki an additional page for initializing UFS can be found [here](https://wiki.linaro.org/Internal/QCOM/DragonBoard-820c). 

## Install LXQt desktop
Installing the LXQt Desktop is done by following the **Graphical image (with GPU)** section in the same document [here](https://github.com/96boards/documentation/wiki/Dragonboard-820c-Getting-Started-With-Linux).

## Install and configure demo applications

This section shows how to install, configure and validate VLC, GKRellm, Gqrx, and IceCast.  Execute the following commands in a terminal on the DragonBoard 820c:

### Install the new packages required:
```
sudo apt-get update
sudo apt-get -y install vlc
sudo apt-get -y install gkrellm
sudo apt-get -y install gqrx
sudo apt-get -y install git
sudo apt-get -y install cmake
sudp apt-get -y install libusb-1.0-0.dev
sudo apt-get -y install build-essential
```
OR install all at once:
```
sudo apt-get update
sudo apt-get -y install vlc gkrellm gqrx git cmake libusb-1.0-0.dev build-essential
```
### Configure the system for the RTL-2832U USB dongle

From a terminal on the DragonBoard 820c, first build and install the RTL-2832U USB dongle driver,

```
git clone git://git.osmocom.org/rtl-sdr.git
cd rtl-sdr/
mkdir build
cd build
cmake ../
make
sudo make install
sudo ldconfig
```
Next reate the rtl-sdr rules. Plug the RTL-SDR dongle into the USB Port on the DragonBoard 820c and execute the following command to determine the VenderID and ProductID of the dongle:

`lsusb|grep Real`

You should get an output similar to the following:
`Bus 001 Device 003: ID 0bda:2838 Realtek Semiconductor Corp. RTL2838 DVB-T`






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
