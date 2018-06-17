# daily noise

Here I share my experiences about small problems where I have found solutions working for me.

## 20180617 How To Check An Ethernet Cable Works For 1GBit

I want to connect all my devices in my living room with ethernet cables. I have a box full of cables I have collected over the
years, but about half of them just say Cat5 or only numbers telling you about their physical specification but not if they work for
1GBit connections like Cat6, Cat5e or enhanced Cat5.

My buddy Mat has given me the tip to use the tool `iperf` to measure the connection. This has solved the problem for me.

This is my setup :

1. I bought an Anker USB3 to RJ45 ethernet network adapter, about 15 Euro at Amazon.de .
2. I use this [iperf](https://iperf.fr/) in Windows 10. 
3. In my notebook I have an integrated 1GBit ethernet network adapter 
4. I configure the external USB3 network adapter to `10.2.2.11` and the internal notebook network adapter to `10.2.2.12`.
5. I start one cmd shell where I run the iperf server with `iperf3.exe -s -B 10.2.2.12`
6. When starting in a second cmd shell the client with `iperf3.exe -c 10.2.2.12 -B 10.2.2.11` , the connection is measured 

FYI : You have to bind `iperf` to the network adapters to tell `iperf` which network adapters you use for what. Then telling
the client the IP address of the server tells `iperf` which route to measure. 

## 20180602 How To Connect 2 Rooms Using WiFi 

My telephone connection is in the corridor, all my tethered devices are in the living room and there is no chance connecting 
corridor and living room with a cable.

For this reason all my tethered devices had to use Wifi to connect to each other via the router in the corridor. I could not use 
cables to connect them to have a 1 GBit connection between them.

I was able to solve the problem by buying a FritzBox 4040 for the living room, which is able to connect via WiFi to the router in
the corridor. Just select Internet in the FritzBox 4040 menu and select 'Use existing WiFi connection'.

Now my living room has its own network (NAT), the uplink is via WiFi to the corridor and the router in the corridor does not see
the network in the living room.

Perfect for me.

