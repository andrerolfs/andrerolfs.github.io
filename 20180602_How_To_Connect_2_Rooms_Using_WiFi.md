{% include_relative menu.md %}

# 20180602 How To Connect 2 Rooms Using WiFi

My telephone connection is in the corridor, all my tethered devices are in the living room and there is no chance connecting
corridor and living room with a cable.

For this reason all my tethered devices had to use Wifi to connect to each other via the router in the corridor. I could not use
cables to connect them to have a 1 GBit connection between them.

I was able to solve the problem by buying a FritzBox 4040 for the living room, which is able to connect via WiFi to the router in
the corridor. Just select Internet in the FritzBox 4040 menu and select 'Use existing WiFi connection'.

Now my living room has its own network (NAT), the uplink is via WiFi to the corridor and the router in the corridor does not see
the network in the living room.

Perfect for me.
