{% include_relative menu.md %}

# 20170525 Raspberry Pi - Developer Tools 3 - Bash Shortcuts

This post is about saving time while typing long commands.

It is about

* executing long commands using short aliases
* executing several commands in one commandline separated with ;
* using the bash history

The problem I had was, I am much slower when it comes to typing on an Ipad compared to a normal keyboard.

My bus rides are too short for spending my time on typing commands for calling tools, I wanted to reduce this time to a minimum.

Instead of creating a dozen new scripts I decided to use the <b>alias</b> feature of <b>Bash</b>.

Bash allows you to create alias commands in <b>.bashrc</b> where you can specify what to do.

These are my current aliases :

    alias bashrc="nano /home/pi/.bashrc"
    alias srcbashrc="source /home/pi/.bashrc"
    alias goblog="cd /mnt/usbstickext4/pi/gitrepo/piblog"
    alias buildblog="gradle compileBlog -PcurrentDir=$(pwd)"
    alias gousb="cd /mnt/usbstickext4/pi"
    alias pull="git pull"
    alias ll="ls -ll "
    alias lsr="ls -R"
    alias tomcat="cd /home/pi/bin/tomcat/bin"
    alias starttomcat="tomcat ; ./startup.sh"
    alias showbashrc="more /home/pi/.bashrc"

When looking at <b>starttomcat</b> you can see, that

* you can call aliases in an alias definition
* you can execute several commands in one commandline by separating them with <b>;</b>
* you can execute several commands in one alias definition

There is another way to shorten commands. When you just want to execute a command, which you have executed recently,
then it is most likely still available in Bash's history list of executed commands.

With

    history

you get an enumerated list of the last commands.

I don't know how long this list can become, mine is currently 560 entries long and the tail looks like this :

    551  git status
    552  git push
    553  git status
    554  gradle compileBlog -PcurrentDir=$(pwd)
    555  git status
    556  gradle compileBlog -PcurrentDir=$(pwd)
    557  gradle compileBlog -PcurrentDir=$(pwd)
    558  git status
    559  history
    560  history

Now you can execute a command by connecting "!" with the entry number, for example

    !560

or you connect "!" with the first letters of the command you want to repeat and the last entry in the
history list starting with these letters will be executed.

Like instead of calling

    gradle compileBlog -PcurrentDir=$(pwd)

I only execute

    !grad

when I know I have only used this Gradle task recently.

I keep the alias for this gradle task in my .bashrc, because I don't want to type this again
on the next Raspbian image where I will not have it in my history when starting this image for the first time.
