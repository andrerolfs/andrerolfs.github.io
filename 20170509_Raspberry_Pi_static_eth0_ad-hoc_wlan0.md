{% include_relative menu.md %}

# 20170509 Raspberry Pi static eth0 ad-hoc wlan0

In my last post I have explained how I did the basic configuration of Jessie light on my Pi.

In this post I configure a static IP address for the ethernet connection to update and install software.

Then I set up an ad-hoc wlan including dhcp server to access the Pi without a router when I am outside.

Let's start with the static IP address for eth0.

In

    /etc/network/interfaces

I write this

    # Ethernet
    auto eth0
    allow-hotplug eth0
    iface eth0 inet static
    address 10.0.0.51
    netmask 255.255.255.0
    gateway 10.0.0.1
    dns-nameservers 10.0.0.1

This means

* configure eth0
* allow plugin the network cable after the Pi has booted
* configure a static address for eth0
* the address is 10.0.0.51
* default netmask
* gateway and dns nameserver are my router 10.0.0.1

After the next reboot the connection works.

My eth0 configurations expects the Pi to be connected to my home network.

Outside this network is not available, so how shall I access the Pi then?

My first try was a mobile router. That works like expected, I can even re-use my eth0 configuration if I connect the mobile router and Pi with a cable.

The problem was the power, the mobile router was not working stable together with the Pi connected with the same power bank and connecting it with a second bank using its second cable connector did not make it more stable.

So I decided to use an ad-hoc connection instead.

for using it with an Ipad it seems to be necessary to have a DHCP server running on the Pi, otherwise the Ipad sees the ad-hoc network, but I was not able to configure an IP address on the Ipad.

I have installed an ISC-DHCP-Server following this manual :

[Elektronik Kompendium : Raspberry Pi: ISC-DHCP-Server einrichten](https://www.elektronik-kompendium.de/sites/raspberry-pi/2202021.htm)

First you install the server :

    sudo apt-get install isc-dhcp-server

Then you edit the server config file with

    sudo nano /etc/default/isc-dhcp-server

to have this line at the end of the file :

    INTERFACES="wlan0"

Now the server knows for which network interface to produce addresses.

Now the DHCP configuration itself needs to be done. For me an address range of 100 - 150 is more than enough since only one device can connect with the ad-hoc network at a time. I could try to reduce the range, but also this would not help much.

I edit dhcp.conf with

    sudo nano /etc/dhcp/dhcpd.conf

and overwrite its content with

    authoritative;
    default-lease-time 86400;
    max-lease-time 86400;

    subnet 192.168.1.0 netmask 255.255.255.0 {
      range 192.168.1.100 192.168.1.150;
      option routers 192.168.1.1;
      option domain-name-servers 192.168.1.1;
      option domain-name "local";
    }

At the end the DHCP server is started with :

sudo systemctl start isc-dhcp-server


This has enabled the server permanently.

Now I can configure my ad-hoc network.

I am following this manual :

[Raspberry Pi Forum : Netzwerk aus Laptop und Raspberry Pi (ohne Routerverbindung)](http://www.forum-raspberrypi.de/Thread-netzwerk-aus-laptop-und-raspberry-pi-ohne-routerverbindung?page=4)

I edit again the interfaces configuration with

    sudo nano /etc/network/interfaces

and add at the end

    #ad-hoc wlan0
    auto wlan0
    iface wlan0 inet static
    address 192.168.1.1
    netmask 255.255.255.0
    wireless-mode ad-hoc
    wireless-essid london-adhoc

After the next reboot I see 3 interfaces up and running :

* eth0
* lo
* wlan0

and I can connect both ways depending on the interface :

    ssh pi@10.0.0.51

via eth0 or

    ssh pi@192.168.1.1

via wlan0.
