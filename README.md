OMXPlayer-Sync
==============

OMXPlayer-Sync facilitates synchronization of multiple OMXPlayer
instances over the network in a master/slave fashion. Fork for Raspberry Pi Zero W.
Tried and works fine on 4 raspberry pi zero W, connect to same wifi router. No need Static IPs


Usage
-----

```
$ ./omxplayer-sync -h
Usage: omxplayer-sync [options] filename

Options:
  -h, --help            show this help message and exit
  -m, --master          
  -l, --slave           
  -x DESTINATION, --destination=DESTINATION
  -u, --loop            
  -v, --verbose         
  -o ADEV, --adev=ADEV  
  -a ASPECT, --aspect=ASPECT  Aspect Mode - fill, letterbox, stretch
```

**Master**

```
omxplayer-sync -m movie1.mp4
```

**Slave**

```
omxplayer-sync -l movie1.mp4
```


Requirements
------------
A recent version of Python3 or Python2.
A recent version of the [python bindings for D-Bus](http://www.freedesktop.org/wiki/Software/DBusBindings).  
A recent build of omxplayer for [Jesse](http://steinerdatenbank.de/software/omxplayer_0.3.7~git20180910~7f3faf6~jessie_armhf.deb) or [Stretch](http://steinerdatenbank.de/software/omxplayer_20180910~7f3faf6~stretch_armhf.deb).


Installation on Raspbian
------------------------

Omxplayer is not anymore on Raspberry Pi Os, so you need to download and Install an Old version of Raspbian. I tried the 2020-02-13-raspbian-buster.zip.
You can download it here : https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2020-02-14/
To install on your Raspberry Pi, use the Raspberry Pi Imager Software : https://www.raspberrypi.com/software/

Once installed, run the Raspberry. Open a terminal window and run that lines :

**For Master and Slaves :**
```
sudo apt-get install libpcre3 fonts-freefont-ttf fbset libssh-4 python3-dbus
sudo wget -O /usr/bin/omxplayer-sync https://github.com/gauthierlerouzic/omxplayer-sync/raw/master/omxplayer-sync
sudo chmod 0755 /usr/bin/omxplayer-sync
sudo wget https://github.com/gauthierlerouzic/omxplayer-sync/raw/master/synctest.mp4
```
* you can also download your video directly, but it's better to use the SyncTest video first to test and debug if it's necessary. Video files need to have exactly the same name and the same frame count.
```
sudo pip install pexpect
sudo apt-get install python-dbus
```

**After installation :**
in terminal :
```
sudo raspi-config
```
Change the Boot mode to command line and auto login.
and change the hostname if you are using ssh, to better read on network.


Start on Master (-u loop, -v verbose, --no-osd No text)
```
omxplayer-sync -muv --no-osd synctest.mp4
```
Start on Slave (-u loop, -v verbose, --no-osd No text)
```
omxplayer-sync -luv --no-osd synctest.mp4
```

**For Auto-play when turn on :**
```
sudo nano .bashrc
```
Add tat the bottom :

For Master : 
```
sleep 10; omxplayer-sync -muv --no-osd synctest.mp4
```
For Slaves : 
```
omxplayer-sync -luv --no-osd synctest.mp4
```



Usage notes
-----------
 * The filename on the master and the slave must be exactly the same.
 * More testfiles with timecodes can be found on [pocketvj.com][http://pocketvj.com/video/small_testfile.mp4].
 * Make sure there are no other files than movie files (e.g. no pictures, no textfiles) in the folder where the movie is, otherwise you may get sync errors.
 * A RJ45 cable must be connected before you start the master, otherwise it will not send sync data to slave.
 * Do not send audio output flags with omxplayer-sync on the slave e.g. /usr/bin/omxplayer-sync -lu -o both /media/internal/video/* 
 * Use videos which are min. 60 seconds or longer
 * If you dont know how to create a h264 mp4 file, visit http://www.online-convert.com. IMPORTANT, click "Disable audio track". Audio can desync
 * Size the video file for the screensize. It's better is omxplayer-sync do not need to resize. Can be a desync problem
 * I was experiencing desync on WiFi, and you can try to modify GOP on your videos using ffmpeg. 
 My example using ffmpeg encoder :
```
ffmpeg -i synctest.mp4 -c:v libx264 -b:v 4M -x264-params keyint=24:bframes=2 synctestGOP.mp4
```
Don't forget to change the file names on your line. 

Example usage
--------------
see this link: https://www.youtube.com/watch?v=Xp6GKFaw0io&feature=youtu.be
by DSPeelJ
my project : https://www.instagram.com/p/CbK-Rejoe-V/
Sources for ffmpeg GOP custom : https://aws.amazon.com/fr/blogs/media/part-1-back-to-basics-gops-explained/
