What is this?
=============

[Jeremy Lightsmith](https://github.com/jeremylightsmith)'s wife is a Montessori teacher. Together they designed a screenless jukebox. The user chooses the music to play with rfid based cards with pictures of the song/album.

We wanted to build one for our son. Jeremy generously said we were free to rip off his hard work, he just had better things to do than be my tech support. More than fair.

Kit
===

Started with Jeremy's list, made modifications

* [Raspberry Pi 3] (https://www.amazon.com/gp/product/B01CD5VC92/ref=ox_sc_act_title_1?ie=UTF8&psc=1&smid=A3FPRP7I8FTOOJ)  $36 replaces ~~[Raspberry Pi 2](http://www.amazon.com/gp/product/B00T2U7R7I?psc=1&redirect=true&ref_=oh_aui_detailpage_o07_s00) $39~~ 

* [USB NFC Card Reader](http://www.amazon.com/gp/product/B00BYKPHSU?psc=1&redirect=true&ref_=oh_aui_detailpage_o04_s00) ~~$18~~ $17

* [Wi-Fi USB Adapter](http://www.amazon.com/gp/product/B003MTTJOY?psc=1&redirect=true&ref_=oh_aui_detailpage_o07_s00) $9 (didn't need, oops. rp3 has built in wifi)

* [SD Card] (https://www.amazon.com/gp/product/B010Q57T02/ref=ox_sc_act_title_7?ie=UTF8&psc=1&smid=ATVPDKIKX0DER) $11 replaces ~~[SD Card](http://www.amazon.com/gp/product/B00M55C0LK?psc=1&redirect=true&ref_=oh_aui_detailpage_o07_s00) $11~~ 

* [USB Flash Drive] (https://www.amazon.com/dp/B00812F7O8/ref=twister_B00JO6RO8C)  $9 (32 gb) replaces ~~[USB Flash Drive](http://www.amazon.com/gp/product/B005FYNSZA?psc=1&redirect=true&ref_=oh_aui_detailpage_o08_s00) $8 (16 gb)~~ 

* Going to design and 3D print an enclosure. ~~[Case for Raspberry Pi 2](http://www.amazon.com/Official-Raspberry-Pi-Foundation-Model/dp/B00ZW4RKFM/ref=pd_sim_147_11?ie=UTF8&dpID=21Vhd3vo7FL&dpSrc=sims&preST=_AC_UL160_SR160%2C160_&refRID=1ZKG2697ATZRSE53RKYA) $9~~ 

* [RFID cards](https://www.amazon.com/gp/product/B01H5L05RY/ref=ox_sc_act_title_4?ie=UTF8&psc=1&smid=A3WEW9HYH3EA1) $21 replaces ~~[Mifare RFID cards](http://www.amazon.com/gp/product/B00NN6UTKY?psc=1&redirect=true&ref_=oh_aui_detailpage_o06_s00) $26~~

* [Card protector sleeves](http://www.amazon.com/gp/product/B00B7TUIFA?psc=1&redirect=true&ref_=oh_aui_detailpage_o04_s00) $7

* Have one ~~[Mini Remote Control](http://www.amazon.com/gp/product/B00RBGB91K?psc=1&redirect=true&ref_=oh_aui_detailpage_o03_s00) $13~~ 

* Have one ~~[Mini USB Cables](http://www.amazon.com/gp/product/B007NLW3C2?psc=1&redirect=true&ref_=oh_aui_detailpage_o02_s00) $9~~ 


~~=> $150~~

=> 110





Installation (putting software on the SD card with another computer)
============

Started with base Raspbian (2016-05-27-raspbian-jessie)

Followed [these steps](https://www.raspberrypi.org/learning/software-guide/quickstart/)
Including using [etcher](https://www.etcher.io/)



First Boot
============
< land at desktop user "pi" >

setup wifi (click icon in upper right (took a min to be ready) chose ssid, entered pass)

change password (open prompt, passwd, need to know default password is "raspberry"

set locale, timezone.

installed VNC

    1. Install VNC server on Raspberry:
         $ sudo apt-get install tightvncserver
    2. Start VNC server:
         $ vncserver
    3. Set VNC Server to autorun

[Autorun steps via adafruit](https://learn.adafruit.com/adafruit-raspberry-pi-lesson-7-remote-control-with-vnc/running-vncserver-at-startup)

run commands

    $ cd /home/pi
    $ cd .config
    $ mkdir autostart
    $ cd autostart
    $ nano tightvnc.desktop

using nano, copy this, and save:

    [Desktop Entry]
    Type=Application
    Name=TightVNC
    Exec=vncserver :1
    StartupNotify=false


Get [vnc viewer](http://tightvnc.com/download.php) on desktop


Headless
============
done: setup dhcp reservation with pi's mac address 
verified: log into pi via desktop?
verified: log into pi via commandline


(currently here)

Music
============
pick some mp3s. 
put them on the usb stick.
plug in speakers.
play some mp3s.

Card Reader
============
figure out card reader
hook card to a song



---------- Jeremy's details below ----------

Setup WiFi
----------

According to [this](https://techblog.willshouse.com/2013/06/11/solved-raspbian-with-edimax-ew-7811un-wifi-adapter-and-802-1x-authentication/)

You should now be able to ssh into your raspberry pi.

Preparing your music
--------------------

The structure for the USB Flash Drive is:

    music/
        1-my song.mp3
        2-my other song.mp3
        3-my album/
            first album song.mp3
            second album song.mp3
    sounds/
        i_am_listening.mp3
        repeating_on.mp3
        repeating_off.mp3
    cards.txt

You can copy the sounds directory from this repository. And the cards.txt file will get generated for you.

You have to populate with music directory. All that's important is that every file or directory starts with a distinct number then dash.


Mount the USB Flash Drive
-------------------------

create the mount

    cd /mnt
    sudo mkdir bigdaddy
    sudo mount -o uid=pi,gid=pi /dev/sda1 /mnt/bigdaddy
    sudo vi /etc/fstab

        add this line:
        /dev/sda1 /mnt/bigdaddy vfat uid=pi,gid=pi,umask=0022,sync,auto,nosuid,rw,nouser 0 0

Try it out, you should see everything on the flash drive under /mnt/bigdaddy

Setting Up Audio
----------------

Run this: 

    sudo apt-get install alsa-utils mpg123

Reboot:

    sudo reboot

Setup the audio port:

    sudo modprobe snd_bcm2835
    sudo amixer cset numid=3 1

Turn up the volume:

    amixer set PCM -- -0000

Update Software & Pull Libraries
--------------------------------

Run this:

    sudo apt-get update
    sudo apt-get install build-essential python-dev git

Downloading our software
------------------------

create an ssh key

    ssh-keygen

add your public key as a deploy key in the project

    git clone https://github.com/jeremylightsmith/rpi-jukebox.git jukebox

try it by

    jukebox/bin/jukebox

<!-- 
sudo apt-get install python-dev python-pip gcc
sudo apt-get install linux-headers-$(uname -r)
 -->

Start on boot
-------------

    sudo vi /etc/rc.local

add this line:

    /home/pi/jukebox/bin/jukebox &
