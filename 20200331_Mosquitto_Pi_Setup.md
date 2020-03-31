{% include_relative menu.md %}

# 20200331 Mosquitto Pi Setup

This is how I have setup and configured my Mosquitto server running on Raspbian Buster lite.

1. write the Raspbian Buster lite image with [win32diskimager](https://sourceforge.net/projects/win32diskimager/) to the SDHC card
2. configure the Raspbian with rasp-config like I have documented [here](https://andrerolfs.github.io/20170426_Raspberry_Pi_raspi-config.html)
3. update the Raspbian with

        sudo apt-get update
        sudo apt-get dist-upgrade

4. install the mosquitto server according to [App Code Labs
 Introduction to IoT: Build an MQTT Server Using Raspberry Pi](https://appcodelabs.com/introduction-to-iot-build-an-mqtt-server-using-raspberry-pi)

5. verify that `/etc/mosquitto/mosquitto.conf` contains the line

        include_dir /etc/mosquitto/conf.d

6. create a `conf.d`directory in `/etc/mosquitto/`

        mkdir /etc/mosquitto/conf.d

7. create a password for a user with the name `user` in `/etc/mosquitto/conf.d/`

        sudo mosquitto_passwd -c mosquitto_passwordfile_user user
   
8. create the file `/etc/mosquitto/conf.d/mosquitto.conf` with this content :

        allow_anonymous false
        password_file /etc/mosquitto/conf.d/mosquitto_passwordfile_user
   
At the end I have achieved this :

1. the mosquitto server is started automatically when Raspbian is booted
2. my `/etc/mosquitto/conf.d/mosquitto.conf` is included by `/etc/mosquitto/mosquitto.conf`
3. the mosquitto server does not allow anonymous login anymore
4. the only allowed user is `user` and you have to use the password coded in `/etc/mosquitto/conf.d/mosquitto_passwordfile_user`

