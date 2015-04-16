# OctoPrint-GPX
An OctoPrint plug-in to use GPX as the protocol layer underneath rather than
replacing g-code to talk to s3g/x3g machines, for example, a FlashForge.

This plugin as it is now written requires brand new hooks that are just barely
in OctoPrint's devel branch, so you'll need to run from source on the bleeding
edge until that makes it into an official release

## Caveats
I've only tested this on one config:
Raspberry Pi set up via the OctoPi image
FlashForge Creator Pro running Sailfish 7.7

## Installing
* Start with OctoPi: Get your Raspberry Pi up and running by following the instructions on [OctoPi](https://github.com/guysoft/OctoPi)

* Switch to the devel branch of OctoPrint
  (https://github.com/foosel/OctoPrint/wiki/FAQ#how-can-i-switch-the-branch-of-the-octoprint-installation-on-my-octopi-image)
```
cd ~/OctoPrint
git pull & git checkout devel
~/oprint/bin/python setup.py clean
~/oprint/bin/python setup.py install
sudo service octoprint restart
```

You may want to test that it still works at this point.  Well, works in that the web page renders.  You won't be able to connect to your x3g printer yet.  To make sure OctoPrint runs:

* Get the GPX plugin
```
cd ~
git clone https://github.com/markwal/OctoPrint-GPX
cd OctoPrint-GPX
git submodule update --init
~/oprint/bin/python setup.py install
```

* Restart OctoPrint
```
sudo service octoprint restart
```

* Create a gpx.ini
You want the gpx.ini to have the settings for your printer.  If you already use
gpx with your slicer, copy it from there.  Otherwise, copy it from the GPX
folder.  It goes in ~/.octoprint/plugins.  I recommend switching the flavor to
reprap.
```
mkdir ~/.octoprint/plugins
cp ~/OctoPrint-GPX/GPX/gpx.ini ~/.octoprint/plugins
```

* Turn on the GPX plugin
From the octoprint UI: choose settings from the navbar at the top, then GPX from
the bottom left, then switch from G-Code to GPX in the protocol, hit save.

* Try connecting
Choose a baudrate.  I don't have AUTO working yet.  115200 works on my bot, but you might want to try slower?

## Known issues
* Upload to SD doesn't work.  It can't work directly because SailFish removed
  that feature to save bytes.  Probably a good call since who wants to wait for
  115200 baud when you can just plug the SD card into your PC.
* Can't delete SD files for a similar reason
* OctoPrint gets confused sometimes when using the LCD panel to make changes,
  we'll work on making it more robust
* Octoprint expects the g-code to be Reprap style.  I haven't run accross all of
  the difficulties with this, but one is that when it runs across M109 it jumps
  to the wrong conclusion about what's going to happen next.
* I really wouldn't recommend printing directly from the gcode file rather than
  an x3g on the SD card.  Besides Sailfish recommends against anything but SD,
  there's also the fact that this is fairly Alpha code and you wouldn't want a
  communications glitch to ruin your print several hours in. And I expect one.
  More than likely pretty quickly, but worst case, just before the build is
  done. 
* Oh yeah, at the moment, its pretty piggy with the log file.  Default location
  is ~/.octoprint/logs/gpx.log.  You might want to delete that from time to
  time.
  
## Plan

I was thinking for my next project, possibly, to start an OctoPrint plug-in
that will talk to the FlashAir SD card directly bypassing the motherboard on
the the bot.

The GCode viewer can't follow along the build on the SD card because it doesn't
have a good way to understand where the bot is in printing an x3g file.  I'm
thinking about this one.  Perhaps we can use the number of commands processed to
line something up.
