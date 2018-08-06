{% include_relative menu.md %}

# 20170525 Raspberry Pi - Developer Tools 2 - Java Gradle Git

In this post I explain how I have set up Java, Gradle and Git on my Raspberry Pi.

My initial reason to start working with the Pi was :

I want to be able to develop, compile and debug my blog and my other software while I am in the bus or in the tube.

Installation of Java and Gradle is the same as under Ubuntu, download and unpack them and put their bin folders into the path in <b>.bashrc</b>.

The Raspbian/Jessie light default image is 32bit, and since I use the 32 bit image I use <b>jdk-8u121-linux-arm32-vfp-hflt.tar.gz</b>.

I don't link it here, better google for <b>Java SE 8 Archive Downloads</b> to find a current version.

I googled reasons for why the default images on the Pi are 32bit, though 64bit is supported. I have found these articles :

[RASPBERRY PI: Der mit dem 64-Bit-Kernel tanzt](https://www.golem.de/news/raspberry-pi-der-mit-dem-64-bit-kernel-tanzt-1611-124475.html)

[Raspberry Pi 3 Still Essentially a 32-Bit SBC For Now](https://www.linux.com/news/raspberry-pi-3-still-essentially-32-bit-sbc-now)

Gradle does not differ the architectures, you get it from [here](https://gradle.org/install).

This is how I have put Java and Gradle into the path in <b>.bashrc</b> :

    JAVA_HOME=/home/pi/bin/jdk1.8.0_121
    GRADLE_HOME=/home/pi/bin/gradle-3.4.1/bin
    export PATH=$PATH:$JAVA_HOME/bin:$GRADLE_HOME

Now to Git. I have set up one server repo (origin) on the Pi, to be able to sync Ipad and Pi :

    $ mkdir gitserver
    $ cd gitserver
    $ mkdir piblog.git
    $ cd piblog.git
    $ git init --bare --shared
    $ cd ..
    $ chmod -R o+w piblog.git

and then I have cloned it on the Pi to use it there

    $ cd /mnt/usbstickext4/pi/gitrepo
    $ git clone ssh://pi@10.0.0.51/home/pi/gitserver/piblog.git

and on my Mac I created a clone the same way and then created from that clone another origin on my Synology as backup :

    $ git remote add piblogorigin ssh://amos@10.0.0.42/volume1/homes/amos/gitrepo/piblog.git
    $ git push piblogorigin
    $ git pull piblogorigin master
    $ git push piblogorigin master
    $ git remote

The output of <b>git remote</b> is now

    origin
    piblogorigin
