---
layout: post
title: Talking to A Pi with help from a Console Cable
tags: [raspberry-pi]
blogpost: true
---
As I don't have a spare working computer monitor at present I was keen to interact with my Raspberry Pis, particularly the Zero Pi which doesn't have an ethernet port. Luckily for me my partner included one of these niffy things among this year's Christmas presents:

![Serial Console Cable for Raspberry Pi]({{site.baseurl}}/images/consolecable1.JPG "Console Cable")

A very handy USB to TTL Serial Console Cable. Although AdaFruit has an [excellent tutorial](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-5-using-a-console-cable) on connecting up via the cable it wasn't as straight forward as I first hoped on a Mac. The main issue was the link to the driver was out of date as it was referring to a older version of the chipset adapter built into the cable. The driver for the current chipset pl2303, can be found [here](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=229&pcid=41) and after installation will require a reboot.

Without the cable connected to a laptop connect up the leads to the Raspberry Pi GPIO header pins on outer edge: red lead to 5V Power Pin, (skip next pin), black lead to Ground Pin, white lead next to black, green lead next to white. It should look like this:

![Serial Console Cable connected to a Raspberry Pi 2]({{site.baseurl}}/images/consolecable2.JPG)

Plug the USB into your laptop and if the installation of the driver was successful typing `ls /dev/cu.*` into a terminal window should show `cu.usbserial`. Use the System Information application to view the USB bus after the cable is connected to check OSX is able to see the device. 

At this point it's possible to 'see' what's happening on the pi using a screen session by typing `screen /dev/cu.usbserial 115200` into the terminal window. If all is well you should see the login prompt and the keyboard should send characters to the pi.

![Serial Console Cable connected to a Raspberry Pi 2 with Screen Session running]({{site.baseurl}}/images/consolecable3.JPG)

Now you can log into your Pi just as you would if you had a keyboard and monitor attached! A list of useful screen commands can be found [here](http://aperiodic.net/screen/quick_reference).