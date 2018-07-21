{% include_relative  menu.md %}

# daily noise

Here I share my experiences about small problems where I have found solutions working for me.

## 20180721 Theme And Menu On GitHub Pages

I have found out how to configure a theme and have a menu on my GitHub pages.

Menu means for me to have a file included in every other file which holds some links, 
so I don't have to repeat myself with linking.

You get the menu by 

1. creating a file like `menufile.md` in your pages repo root directory
2. write into the menu file the markdown code you want to use as menu
3. include ```{% include_relative menufile.md %}``` where you want to integrate this menu code

You get the theme by clicking to `Settings` in your repository, click `change theme`and select one.
This changes the file `_config.yml`.

I have chosen the midnight theme, the tool has added this code in my `_config.yml` :

    theme: jekyll-theme-midnight
    
This works since it is an official theme and the Jekyll framework knows how to get from this configuration 
to this offical theme.

I have read you can configure any other user repo providing themes bye using `remote_theme: owner/name` like
explained in [The GitHub Blog](https://blog.github.com/2017-11-29-use-any-theme-with-github-pages/) but I did not test this.

## 20180707 HTML Framework In Groovy For Creating Tables

I have spent some time on my [HTML framework](https://github.com/andrerolfs/htmlframework). 

Now I can create tables in HTML with `<table/>`and `<div/>` by writing a script in groovy which has to  create a
hierarchy of objects representing the structure and content of the table. 

Please look at the Spock unit tests to see how to use it.

The example for `<table/>` is :

```
    HtmlTable table = new HtmlTable()
    table.setColumns(2)
    table.addCell("Hello 1")
    table.addCell("World 1")
    table.addCell(new HtmlA("lo","http://hel"))
    table.addCell("World 2")
    String output = HtmlTooling.htmlText( 
                        table, 
                        "/Users/amos/github/htmlframework/css/divtable.css")
```

## 20180624 How To Open A Series Of HTML Files In Firefox In The Same Tab Without Using The GUI

I want to create debug output as HTML files and then automatically load these html files in Firefox, but always use the
same tab, so I don't have to close tabs with content I don't need anymore.

By sending an URL request to Jenkins I can execute scripts, so I just need a link to Jenkins and click it to execute the Jenkins script.

The Jenkins script first executes my tool which creates the HTML file. Then the Jenkins script starts Firefox with the HTML file as parameter, e.g. in Windows it could be `"C:\Program Files (x86)\Mozilla Firefox\firefox.exe" abc.html`.

This would usually open a new tab in Firefox, so I would have to close these tabs manually when there are too much of them.

I have found this [hint](https://superuser.com/questions/138298/force-firefox-to-open-pages-in-a-specific-tab-using-command-line) how to configure Firefox to re-use the current tab for externally opened files on
[StackExchange](https://stackexchange.com/).

1. Enter about:config in the address bar
2. Click the confirmation button if shown
3. Filter for browser.link.open_newwindow
4. Double click
5. Set value to 1
6. OK

This is the Mozilla documentation of the switch : [Browser.link.open newwindow](http://kb.mozillazine.org/Browser.link.open_newwindow)

Now I can do this :

1. Open Firefox
2. Build a job in Jenkins which creates a HTML file and loads it in the current tab
3. click a link in my HTML file which triggers to build a Jenkins job which builds another html and loads it in the tab

This means, when I have finished [my framework for creating HTML files](https://github.com/andrerolfs/htmlframework) I can
use Jenkins for two things :

1. for debugging my tool
2. for being my web application to show me the debug information in HTML

Yes, this may be slower than a real web application using a database and yes, the HTML pages will never look as nice as pages created with a professional framework, but I don't need to build any binaries and can adapt the code for creating the debug information and writing it to html on-the-fly while debugging.

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

