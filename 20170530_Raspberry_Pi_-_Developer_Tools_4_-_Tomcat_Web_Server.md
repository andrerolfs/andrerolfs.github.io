{% include_relative menu.md %}

# 20170530 Raspberry Pi - Developer Tools 4 - Tomcat Web Server

Working on a software generating HTML pages makes it necessary to test these pages
with a real web server.

I use Tomcat on my Raspberry Pi to test the pages on my Ipad.

You get Tomcat [here](http://tomcat.apache.org/whichversion.html), I currently use version 7.0.72 .

I have downloaded the tar.gz archive and unzipped it to <code>/home/bin/tomcat</code>.

I don't want to run Tomcat as a service, but I want to have it started at boot-time, so I
have added this line at the end of <code>.bashrc</code> :

    /home/pi/bin/tomcat/bin/startup.sh

FYI :

1. My first try was to do it with a cronjob with @reboot, but that did not work. Googling showed that this seems to be a common problem and that it could take longer to find out why. I will have a look at cronjobs later.
2. I have tested that starting an ssh session only starts a new webserver when it was shut down before, for now this hack is ok for me.

To access static HTML pages I have put these <code>Context</code> lines at the end of the <code>host</code> block :

            ...
            <Context docBase="/home/pi" path="pi" />
            <Context docBase="/mnt/usbstickext4/pi/gitrepo/piblog/outputHTML" path="/blog" />
          </Host>
        </Engine>
      </Service>
    </Server>

This enables Tomcat to serve my home on the Pi under <code>/pi</code> and my built blog pages under <code>/blog</code>

This is an example URL for <code>/pi</code> to read a log file located in the home folder :

    http://10.0.0.51:8080/pi/pibashrc.log

and this is how I access the blog on the Pi :

    http://10.0.0.51:8080/blog/index.html
