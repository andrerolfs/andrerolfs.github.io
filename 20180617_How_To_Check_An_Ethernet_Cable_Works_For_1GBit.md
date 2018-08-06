{% include_relative menu.md %}

# 20180617 How To Check An Ethernet Cable Works For 1GBit

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
