# streaming-radio
This is primarily an instructional repo that outlines the steps to use IceCast and Gqrx on DragonBoards 820c (herein referred to synomously with "the target") in creating a streaming SDR.

# Initial Setup
This demo uses an RTL-SDR USB audio dongle based on the RTL2832U chipset. The good news is these are only $20! The Gqrx open source application connects to the dongle and decodes full-spectrum radio including FSRS, Amature Radio bands, FM Radio, National Weather frequencies, Air Traffic control, and more.   It then leverages the streaming UDP output of Gqrx, uses VLC to convert this to Ogg and sends it to the open source streaming audio server application IceCast, creating a streaming radio that streams the radio frequencies being played to client computers.   The high level demo setup is shown in the picture below.

![alt text](Images/Qualcomm-DragonBoard820c-StreamingRadioDemo.jpg "Demo Setup")

* Information on the UART Serial Mezzanine can be found [here](https://www.96boards.org/product/uartserial/)
* Information on the DragonBoard 820c can be found **tbd**'
* I used [this](https://www.amazon.com/gp/product/B01N905VOY/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) USB Audio Adaptor
* And I used [this](https://www.amazon.com/RTL-SDR-Blog-RTL2832U-Software-Telescopic/dp/B011HVUEME/ref=sr_1_34?ie=UTF8&qid=1502428142&sr=8-34-spons&keywords=rtl-sdr+usb&psc=1) SDR Dongle

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
sudo apt-get -y install git
sudo apt-get -y install cmake
sudp apt-get -y install libusb-1.0-0.dev
sudo apt-get -y install build-essential
```
OR install all at once:
```
sudo apt-get update
sudo apt-get -y install vlc gkrellm git cmake libusb-1.0-0.dev build-essential
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

From the above, determine the VenderID and Product ID.  In the example above, the VenderID is `0bda` and the ProductID is `2838`. 

Create the rtl-sdr.rules file:

`vi /etc/udev/rules.d/rtl-sdr.rules`

And insert the following line:
`SUBSYSTEMS=="usb", ATTRS{idVendor}=="**0bda**", ATTRS{idProduct}=="**2838**", MODE:="0666"`

Save the file and exit.

Power cycle with the SDR USB Dongle disconnect and **after** the system has booted, install the dongle.

To test the driver build above and assure it's working correctly, run the following command:

`sudo rtl_test -t`

Successful results should look similar to the following:

```
sudo rtl_test -t
Found 1 device(s):
  0:  Realtek, RTL2838UHIDIR, SN: 00000001

Using device 0: Generic RTL2832U OEM
Found Rafael Micro R820T tuner
Supported gain values (29): 0.0 0.9 1.4 2.7 3.7 7.7 8.7 12.5 14.4 15.7 16.6 19.7 20.7 22.9 25.4 28.0 29.7 32.8 33.8 36.4 37.2 38.6 40.2 42.1 43.4 43.9 44.5 48.0 49.6 
[R82XX] PLL not locked!
Sampling at 2048000 S/s.
No E4000 tuner found, aborting.
```
Don't worry about the ominous last three lines above. Things work fine with those.  If you happen to run the rtl_test command while Gqrx is running the output is a little different as follows:

```
Found 1 device(s):
  0:  Realtek, RTL2838UHIDIR, SN: 00000001

Using device 0: Generic RTL2832U OEM
usb_claim_interface error -6
Failed to open rtlsdr device #0.
```

Again, don't worry about the ominous last two lines above.

### Verify sound
Before installing Gqrx, let's go ahead and verify that audio is working.  

* Copy any .mp3 or .ogg audio file (a song) to the target. 
* Plug in the USB Audio Adaptor into the target and plug a set of headphones (or 3.5mm jack speaker) into the USB Audio Adaptor
* Bring up VLC found in the `Sound & Video` sub menu on the desktop.
* Play the song that was downloaded and verify that you can hear it.

If you cannot hear the song playing on the headphones, then open the `PulseAudio Volume Control` sub menu to bring up the audio configuration application as shown below:

![alt text](Images/Qualcomm-DragonBoard820c-StreamingRadioDemo.jpg "Pulse")


Debug the configuration to make sure the USB Audio Adaptor is the default sound device. Don't continue until sound is working on the target.

### Install Gqrx
As a reminder, these instructions are sequential and assume you have performed all steps successfully up to this point.

Install Gqrx:

```
sudo apt-get update
sudo apt-get -y install gqrx-sdr
```
Optimize the performance by running the following:

```
sudo apt-get install libvolk1-bin 
volk_profile
```
The above takes a while. You are now ready to fire up Gqrx and listen to the air waves!  But first let's get installing and configuring IceCast out of the way.

### Install and Configure IceCast





# Set up the demo
Now that everything is installed, we understand how to check audio by using the PulseAudio Volume Control App, we can set up the demo.  This is the section that needs to be done each time you power up the demo.




# Debug hints
Helpful debug hints when setting up and verifying this demo:

### Check status of icecast

`ps -aux|grep icecast`

### IceCast commands from DragonBoard 820c terminal:

```
/etc/init.d/icecast2 start
/etc/init.d/icecast2 stop
/etc/init.d/icecast2 restart
```

### Checking to see if Gqrx UDP is streaming:

`nc -l -u localhost 7355`  or `nc -l -u 7355` or `nc -l -u <IP Address of 820c> 7355` 

Can be entered from the terminal to see the UDP streaming from Gqrx.When executed from a terminal on the target, the above will create a random character stream in the terminal window on the target. Note there may be cases where one of these will work and another will not. 

### Monitoring board temperature

Note that since the kernel is not entirely integrated at the time of this writing, it's a very good idea to monitor and control the temperature on the board.

Two options were used to monitor temperature

1) From Command Line enter:

`cat /sys/class/thermal/thermal_zone*/temp`

There are four zones that will come up similar to the following:
```
cat /sys/class/thermal/thermal_zone*/temp
41600
41900
42900
42900
```
At the time of this writing, I don't know where these 4 zones are on the board.
Divide each of the above by 1000 to come up with degrees celsius.

2) GKrellm (Preferred)
We already installed GKrellm, so just start it up and configure it to show thermals.  It will list all 4 zones in Celsius

To control and minimize the risk of overheating, expecially with an application like Gqrx that requires a lot of processor power to perform the frequency transforms in software, it is recommended at this time to add similar heat sinks and have a small fan cooling the board.  See setup picture below.
    
    
PICTURE HERE




# References
* [The Gqrx SDR home page](http://gqrx.dk/)
* [Setting up RTL-2832U USB Dongle for Raspberry Pi](http://zr6aic.blogspot.ca/2013/02/setting-up-my-raspberry-pi-as-sdr-server.html) was very helpful.
* [Youtube video]()https://www.youtube.com/watch?v=4kJc1aKg17c&t=8s that was helpful in connecting Gqrx to IceCast using VLC
* How the UDP Streaming works on Gqrx; a nice writeup [here](http://gqrx.dk/doc/streaming-audio-over-udp#more-157)
* 


# Extra Credit 
If you got this far, congratulations!  You are streaming radio over your home network, and with minor modifications, even over the internet!  This section lists some possible follow-on extensions to the project that could be fun.
* Build and use a gstreamer pipeline instead of VLC to convert the output of Gqrx into an IceCast source stream. I've given it a try, but unsuccessful to date.  Ping me if you want my gstreamer pipelines I have attempted to no avail.
* Use other SDR's instead of Gqrx.
* Test with some different SDR Dongles other than the one used here.
* Build Gqrx from source and redo the demo.  This would be the first step in investigating ways to do some performance improvements.
* Use more elaborate and advances antennas to improve reception.
* Create a scanner.  Some starting code can be found for this in the community.
* Punch through your firewall and stream to the internet.
* Build your DragonBoard 820c from source.  Also could build and Open Embedded (OE) image and repeat the demo.
* others?
