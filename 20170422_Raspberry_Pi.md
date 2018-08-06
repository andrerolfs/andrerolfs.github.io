# 20170422 Raspberry Pi

I have started to work with a Raspberry Pi.

The idea is to use it for compiling my blog and my software, when I am not at home, e.g. when I am in the bus.

I am using my Ipad in combination withe Pi like a complete computer, the pad is keyboard, monitor and shell, the Pi does the work.

I will write posts about how to setup the Pi and the software on it, one article for each aspect. This way I hope to publish more often in my blog.

Here I want to give you an outlook, what will come :

* Setup the Pi with Jessie Light and how to configure it with raspi-config
* Setup eth0 with a static IP address to update the Pi and install software
* Setup wlan0 as ad-hoc network to connect with the Ipad without router
* Setup ProFTP to load downloaded tools to the Pi
* Setup .bashrc with an alias for every tool and folder I often need
* Setup Java, Gradle and Git for work
* Setup a Tomcat web server, to see the blog and to read logs on the Ipad
* Setup a USB stick as backup device
* Setup a USB stick as disk formatted with ext4 to use git on it

What I can tell you already now is, it is a lot of fun for me. Raspberry Pi 3 is a really mighty thing, it is much faster than expected, my blog compiles in 40 seconds and I have increased the compilation effort drastically, because now every post is an own file for which pandoc needs to be called. I have splitted my index.html ... but that is the topic of another post.

To power the Pi while I am out of home, I use a power bank from EasyAcc with 20000 mAh. I made test, but after 30 hours I stopped it, because 2 of the 4 lights telling you about how much power is left were still on.

Show me one Ultrabook with more than 30 hours runtime where you get mobile power blocks for 30 Euros :)

My Ipad runs for about 9 hours and I can load it as well with a power bank.

The last point to mention here : When you intend to use a notebook as tablet, this will work very good for you, if your apps are made for the tablet mode. When you use a pad, and here the OS does not matter, this will just work, because in pads there is no desktop mode!
