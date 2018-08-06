{% include_relative menu.md %}

# 20170525 Raspberry Pi - Developer Tools 1 - FTP Server

My main reason to start working with a Raspberry Pi was to to use it for development while being outside.

My development tools are currently Java, Gradle, Git and my Ipad. For verification and testing I am using a Tomcat web server.

For the development of [CISystem](CISystemRequirements.html) there will be further tools I may need.

To set up these tools I need to get them on the Pi.

To use them on the Pi via typing on my Ipad I need to reduce the amount of needed typing.

There are so many topics to look at when setting up these things, which have no direct connection and which
I don't want to connect in only one post, otherwise you would have to wait months for this post.

So I decided to write separate posts and to connect them under the topic [RASPBERRY PI - DEVELOPER TOOLS](topic_RASPBERRY_PI_-_DEVELOPER_TOOLS.html).

This is the first post where I explain why and how I have installed the ProFTP server on my Pi.

In my last post I have explained how I have configured the network devices of my Pi, a static IP address
for eth0 and an ad-hoc network on wlan0.

Now I want to be able to get Java and Gradle and other tools on the Pi.

I cannot download them via browser, I don't have a desktop installed and don't want to find out for every tools if it is possible
to download it with a console ftp client.

I want a solution which works for all tools, I have installed ProFTP as server, so I can download the tools on my notebook with
a browser and then transfer them using an ftp client on the notebook connected with the ftp server on Pi.

The installation of ProFTP is simple :

    sudo apt-get install proftpd

In case you have never used ftp, use the IP address of your Pi to tell your ftp client how to reach the ftp server
and use the credentials of the pi user to log in.
