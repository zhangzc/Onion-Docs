---
title: Running a Command on Boot
layout: guide.hbs
columns: two
devices: [ Omega2 ]
order: 1
---

## Running a Command on Boot {#running-a-command-on-boot}

This article will demonstrate how you can have your Omega run commands on boot. This can be used in a number of applications, such as showing a welcome message on the OLED Expansion when the Omega has booted or connecting to a server of your choosing, etc. The Omega can get commands to run on boot quite easily, so let's get started!


### Implementation

#### Writing a Script to Run on Boot

We'll first need to write a script that will run on boot. Let's create a file in the `/root` directory and call it `rgb-led`:

```
vi /root/rgb-led
```

Now let's write a small shell script that will flash your Expansion Dock's RGB LED red, then green, then blue, and just in case you miss it the first time, it'll do it once more after waiting for 5 seconds, and then shut off the RBG LED.

Here's what that looks like:

```
#!/bin/sh -e
expled 0xff0000 #Red
expled 0x00ff00 #Green
expled 0x0000ff #Blue
sleep 5 #wait five seconds
expled 0xff0000 #Red
expled 0x00ff00 #Green
expled 0x0000ff #Blue
expled 0x000000 #Off
```

Copy the above code into your file, then save and exit the file:

Next, from your command-line, enter the following:

```
chmod +x /root/rgb-led
```

This command will allow your script to be run as a program.



#### Editing the `/etc/rc.local` File

The Omega reads the commands to run on boot from the `/etc/rc.local` file.

 Type `vi /etc/rc.local` and you'll see the contents of the file:

```
# Put your custom commands here that should be executed once
# the system init finished. By default this file does nothing.

exit 0
```

Here we can see that the commands in this file will be executed after the system initialization, and that he only command being run is `exit 0`. Let's change that and get the RGB LED on the Expansion Dock to change colors on boot.

To do this, edit your `/etc/rc.local` file to look like this:

```
#!/bin/sh -e
# Put your custom commands here that should be executed once
# the system init finished. By default this file does nothing.

/root/rgb-led

exit 0
```

We started by adding `#!bin/sh -e`, known as a "Shebang". This instructs the program loader to run the script as a shell script.

The next thing we add is the command we want to run:

```
/root/rgb-led
```

Save and exit your file, and reboot your Omega to see the effects!


#### Text Output

When `/etc/rc.local` runs on boot, you won't be able to see any output from your file. You may need to see output for debugging purposes to see where your code is failing.

You can pipe the output of your command to a specific destination with a simple addition to your `/etc/rc.local` file.

The syntax for piping your command to a file is as follows:

```
<COMMAND> >> <<OUTPUT FILE>> 2>&1
```

// explain what 2>&1 means

To apply this to your command on boot, simply edit the `/etc/rc.local` file as such:

```
#!/bin/sh -e
# Put your custom commands here that should be executed once
# the system init finished. By default this file does nothing.

/root/rgb-led >> /tmp/output.txt 2>&1

exit 0
```

Looking at `/tmp/output.txt` we see:

```
Setting LEDs to: 000000
Duty: 100 100 100
> Set GPIO17: 1
> Set GPIO16: 1
> Set GPIO15: 1
```
and if we run the command `/root/rgb-led` in the command line we should see the same output:

```
root@Omega-2757:/tmp# expled 0x000000
Setting LEDs to: 000000
Duty: 100 100 100
> Set GPIO17: 1
> Set GPIO16: 1
> Set GPIO15: 1
```

### Troubleshooting

If your commands don't seem to be working on boot, try copying them directly from your `/etc/rc.local` file and running them manually.