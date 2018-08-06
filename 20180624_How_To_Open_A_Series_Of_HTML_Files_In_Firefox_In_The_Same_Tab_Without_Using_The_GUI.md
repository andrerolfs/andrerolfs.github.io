{% include_relative menu.md %}

# 20180624 How To Open A Series Of HTML Files In Firefox In The Same Tab Without Using The GUI

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

