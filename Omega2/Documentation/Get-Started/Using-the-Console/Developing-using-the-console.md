---
title: Developing using the Console
layout: guide.hbs
columns: two
devices: [ Omega2 ]
order: 4
---


# Developing using the Console

The Console is a powerful tool. You can use various apps to write code and execute it, all from your browser!

In this tutorial we're going to write a script that will blink the Omega's LED in morse code based on the user's input using the Editor App, and the Terminal App.


# Overview

| Overview |
| :---: | :---: |
| Tutorial Difficulty | **Intermediate** |
| Time Required | **15 mins** |
| Required Materials: | <ul><li>Omega2 or Omega2+</li><li>Expansion Dock</li></ul> |


# Installing the Editor and Terminal Apps

To install the Editor App, click on the Editor icon in the taskbar. You'll see a page that looks like this:

![editor-app-installation-page](../img/developing-pic-1.png)

Click the `Install` button and wait while the Editor App is installed. The installation takes 2-3 minutes. When the app has been installed you'll see this:

![editor-app-installed](../img/developing-pic-2.png)

Refresh your browser and you can now open the Editor App.

![editor-app-page](../img/developing-pic-3.png)


Now, repeat this process for the Terminal App. Click on the Terminal icon in the taskbar and you'll see a similar page to the Editor's:

![terminal-app-installation-page](../img/developing-pic-4.png)

Click the `Install` button and wait while the Terminal App is installed. The installation takes under a minute. When the app has been installed you'll see this:

![terminal-app-installed](../img/developing-pic-5.png)

Refresh your browser and you can now use the Terminal App.

![terminal-app-page](../img/developing-pic-6.png)


You are now ready to develop code for your Omega from your browser!


# Controlling the LED from the Terminal App

Open the Terminal App and log in using your username and password. The defaults are:

```
username: root
password: onioneer
```

Copy the following command and paste it into your Terminal:

```
opkg update && opkg install kmod-ledtrig-morse
```

To paste into the Terminal app, use `ctrl+shift+v`

Once this is done, a kernel module that can translate text to Morse code and blink the LEDs is automatically installed.  But you still need to tell the kernel which LED you want to blink.  The kernel exposes a lot of hardware status and configuration options through a virtual filesystem under `/sys`.  (I.e. the files under `/sys` aren't *actually* files, but they look and act like files to make it very easy to access them from the command line and in scripts or programs.)

To tell the kernel that we are going to use the new Morse code module, set the LED trigger condition for the Onion system LED to `morse` by using the `echo` command to write the setting into the virtual file:

```
echo morse > /sys/class/leds/onion\:amber\:system/trigger
```

You can verify that it worked by using `cat` to look at the virtual file:

```
root@Omega-2757:~# cat /sys/class/leds/onion\:amber\:system/trigger                                                              
none mmc0 timer default-on netdev transient gpio heartbeat [morse] oneshot
```

You can see that `morse` is selected because it's in brackets.  The text in that file shows the other available options that this particular bit of the kernel can be set to.

Anyway, now we have everything set up!  We just need to tell the kernel what message to blink on the LED.  Conveniently, once the morse option is selected, the kernel creates a new virtual file for that called (unsurprisingly enough) `message`.  We can use `echo` again to put text there:

```
echo Hello, Onion > /sys/class/leds/onion\:amber\:system/message
```

Now watch your LED!  If it's too fast or too slow, you can change the speed with the `delay` file that also gets created.  E.g.

```
root@Omega-12D9:~# cat /sys/class/leds/onion\:amber\:system/delay 50
```

That's pretty fast!  Let's slow it down a bit so that people like me who aren't experts can read it:

```
root@Omega-12D9:~# echo 100 > /sys/class/leds/onion\:amber\:system/delay
```

The message will keep looping forever or until you change it.  To stop it, you can either clear the message entirely:

```
echo > /sys/class/leds/onion\:amber\:system/message
```

or change the LED trigger to something else:

```
echo default-on > /sys/class/leds/onion\:amber\:system/trigger
```

# Writing a Shell Script in the Editor App

```
#!/bin/sh

# main function to read user input
_UserInputMain () {
	echo "Onion Omega Morse Code"
	echo ""
	echo "Enter a message to blink in morse code!"
	echo ""
	echo -n "Message: "
	read input


	echo morse > /sys/class/leds/onion\:amber\:system/trigger
	echo 100 > /sys/class/leds/onion\:amber\:system/delay
	echo $input > /sys/class/leds/onion\:amber\:system/message
}


########################
########################
##### Main Program #####

	_UserInputMain

	exit

```



// this article will show how you can use the console to develop code for the Omega using the Omega (pls reword so this makes sense)
// as an example project, we're going to write a script that will blink the Omega's LED in morse code based on user input

// section on using the editor to create a bash script
//  - installing the editor app
//  - small background on bash scripting
//  - walkthrough on navigating the file system and creating a new script
//    - make sure to mention that the best place for project files is in /root (since it won't be overwritten during firmware updates)
// - explanation of a script that controls the Omega's LED
//    - setting the led trigger to morse code (`echo morse > /sys/class/leds/onion:amber:system/trigger`)
//    - getting input from command line argument for the text to be converted to morse code
// note that there's an article about this already, can borrow heavily

// section on using the terminal app
//  - installing the terminal app
//  - logging in to the terminal
//  - navigating through the filesystem
//    - cd and ls commands, introduce ls -l
//    - have links to getting started with linux - check existing linux basics articles for these links
//  - using the echo command to read the available triggers in `/sys/class/leds/onion:amber:system/trigger`
//  - running the script we wrote using the editor app
// make sure to point out that the terminal app now supports copy and paste (but with weird shortcuts)