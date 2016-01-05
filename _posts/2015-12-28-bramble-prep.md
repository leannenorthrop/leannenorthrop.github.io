---
layout: post
title: Bramble ~ Preparing an OS, Arch Linux ARM
tags: [raspberry-pi, bramble, arm, archlinux]
blogpost: true
---
As I don't have a case to securely hold the bramble together at present I thought now would be a good time to prepare the OS image I would need on each node. My partner, who is curious about the topic, struck on a 'faster' Linux distribution built to run on the Broadcom ARM 7 processors on Raspberry Pi 2 models, [Arch Linux Arm](http://archlinuxarm.org/platforms/armv7/broadcom/raspberry-pi-2]). It's a nice Linux distribution that takes me back to my first days of using Slackware Linux in the late '90's. Also as a nice addition it's a rolling update.

Creating an image for my collection of class 10 SD cards on OSX El Capitan turned out to not be straight forward with little hurdles along the way. I started out by creating a Virtual Box Ubuntu VM so that I could work in a sandbox environment but sadly the SD card reader isn't exposed as a USB device to Virtual Box and so creating partitions wasn't possible. Using OSX's native Disk Utility also proved problematic as I could not use the Partition button on the SD Card Reader device. Not wanting to use OSX command line, the solution was to use a trial edition of [Parallels Desktop for Mac](http://www.parallels.com/uk/products/desktop/) which thankfully correctly shared the SD card. If I knew I was going to be using Parallels in anger then I would consider purchasing a copy.

Using Parallels I was able to attach the SD card and create 3 partitions using GParted as follows: 

1. 100M /boot partition
2. /root partition of aproximately 6 GB
3. swap partition of 1024 MB.

Then following the (Arch Linux Arm installation instructions for Raspberry Pi)[http://archlinuxarm.org/platforms/armv7/broadcom/raspberry-pi-2] I mounted the boot and root partitions of the SD card and extracted the latest Arch Arm root filesystem to the /root partition. I then copied over the files from the /root/boot folder to the /boot partition and then umounted the partitions and unattached from Parallels and ejected from OSX.

After putting the new SD card into one of the Pis I then hooked up the Pi using a serial console cable. My first attempt with wired ethernet networking proved to be unsuccessful so after irrevocably messing up my network settings I decided to re-write the SD card with a fresh install. Somehow the second attempt just worked from a completely fresh install with no tinkering so I was happy.

If I had more time I would take time to configure all the network, user accounts, overclocking etc. to get it 'just right' now so that I don't have to repeat the process on each node but this holiday season is turning out to be busy and it's a good excuse to learn Ansible. So happy that the OS works I simply [enabled swap partition](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/3/html/System_Administration_Guide/s1-swap-adding.html), did a system update (`pacman -Syu`), installed sudo and powered down the Pi, ready to make a raw disk image that I could write out to the other 5 SD cards.

To clone the SD I popped it into the SD card reader on my Mac and ran (don't run this on your computer unless you confirm that the SD card is definitely /dev/rdisk2 otherwise update to match your setup as you don't want to ruin your computer's hard drive):

<pre><code class="hljs bash">dd <span class="hljs-keyword">if</span>=<span class="hljs-regexp">/dev/</span>rdisk2 <span class="hljs-keyword">of</span>=.arch-arm-linux.img bs=<span class="hljs-number">1</span>m
</code></pre>

This put a raw disk image file in the directory I was working from and it was a straight forward task to clone onto each of the remaining 5 cards using (again don't run this on your computer until you are sure that the SD card is mounted as /dev/rdisk2):

<pre><code class="hljs bash">dd bs=<span class="hljs-number">1</span>m <span class="hljs-keyword">if</span>=./arch-arm-linux.img <span class="hljs-keyword">of</span>=<span class="hljs-regexp">/dev/</span>rdisk2
</code></pre>

Popping the fresh cards into each of the PIs I'm eager to get a case together (and a network switch) so I can fire up the bramble...will have to exercise patience.


