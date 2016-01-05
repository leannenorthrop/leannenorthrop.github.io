---
layout: post
title: Bramble ~ Clustering 6 Raspberry Pi 2, Static IPs and OSX Internet Sharing
tags: [raspberry-pi, bramble, arm, archlinux]
blogpost: true
---
The bargin 8 port network switch for £8 arrived today along with my Mac ethernet adapter and it's finally possible to test out the bramble for the first time. Hurrah! So to create this mini cluster I have 6 of almost everything:

* 6 Raspberry Pi 2
* 6 USB to mini USB power cables
* 6 port USB charger with enough power to each port yet regulated power supply
* 6 class 10 32GB SD cards
* 6 cat 5e ethernet cables with snag protection
* 1 8 port 100Mbs unmanaged network switch with power saving a spare slot for my laptop connection and a spare slot for chaining or another Pi. It's so small!

![Parts for Raspberry Pi Cluster]({{site.baseurl}}/images/brambleparts.JPG)

I started by writing out the Arch Linux Arm image I [prepared a couple of days ago]({{site.baseurl}}/2015/12/28/bramble-prep.html) to each of the SD cards which turned out to be quicker than I expected and completed in under an hour.

Hooking the Pis up to power and network switch was straight forward and with intrepidation I powered them up. A satisfying glow of twinkling red lights appeared. As we don't have a Christmas tree this year this was the closest we will get to christmas lights.

The next issue was how to 'talk' to each Pi as none of them have a wireless connector and the switch wasn't connected to our broadband router. Each had a self assigned address and it wasn't possible to quickly find out the IP address. So I decided to have that warm fuzzy feeling of knowing what host is on what IP address and go down the route of static IP addresses for each node.

## Setting Up Static IP on Arch Linux Arm

It took a little while to figure out how to assign a static IP address in Arch Linux as there's a few different sets of software that manages network connections which if all enabled at the same time conflict with each other. Eventually I figured out that with a fresh install of Arch Linux having touched nothing else all I needed to do was update the configuration in the /etc/systemd/network/eth0.network file to include a Network section with the address and and a gateway. I decided to use the 192.168.1.x range of addresses so the /etc/systemd/network/eth0.network file on each Pi looks like this (except the address line differed on each Pi e.g. Address=192.168.1.2/24, Address=192.168.1.3/24, Address=192.168.1.4/24 for the first 3 nodes.):

<pre><code>
[Match]
Name=eth0

[Network]
Address=192.168.1.2/24
Gateway=192.168.1.1
</code></pre>

 The gateway address is the address I wanted my Mac to have (more on that below). I also updated the [/etc/hostname file](https://wiki.archlinux.org/index.php/Beginners'_guide#Hostname) to assign names e.g. strawberry-pi, custard-pi etc. and rebooted each Pi node.

### Setting Up Static IP on OSX

I was hoping that simply connecting my Mac to the network switch would be sufficient but I needed to tinker a bit. So I opened up System Preferences, Network settings and using the + button at the bottom of the list added in the Thunderbolt Ethernet interface and named it Pi Cluster. To enable communication I had to set the Configure IP4 to Manually and pop in my chosen IP address in the 192.168.1.x range of 192.168.1.1, setting 255.255.255.0 in Subnet Mask is helpful as well as using the Advanced button to add in Google's DNS servers to the DNS tab.

![OSX Manual Ethernet Connection Settings]({{site.baseurl}}/images/clusterosxnetworksettings.png)

With that I could happily ssh into each Pi but the manual setting caused me to lose my Internet connection as Safari etc was attempting to access the internet via my wired switch rather than via wireless router. Thankfully it's simple to fix via the System Preferences Network tab by using the Set Service Order to move the wireless interface above the wired interface:

![OSX Set Service Order Menu Option]({{site.baseurl}}/images/clusterosxserviceorder.png)

Service order changed to:

![OSX Set Service Order Menu Option]({{site.baseurl}}/images/osxserviceorder.png)

So now with internet restored on my Mac I quickly realised I couldn't use pacman to install packages on each Raspberry Pi without setting up internet sharing on my Mac. Again I thought it would be a straight forward matter of simply turning on network sharing in the System Preferences Sharing tab but no.....

## OSX Sharing Internet to Static IPs

As it turns out enabling Internet Sharing on OSX fires up a DHCP server which assigns devices IP address in the range of 192.168.2.x. What a pain! As there is no easy dialog to change this IP address range it required a little tinkering around in the terminal window to update the /Library/Preferences/SystemConfiguration/com.apple.nat.plist file. I created a backup and copied the file to my home directory and edited it with Sublime Text.

```
sudo cp /Library/Preferences/SystemConfiguration/com.apple.nat.plist /Library/Preferences/SystemConfiguration/com.apple.nat.backup
sudo cp /Library/Preferences/SystemConfiguration/com.apple.nat.plist ~
```

To assign the network address range you need to add SharingNetworkNumberStart settings but for El Capitan you also need to set SharingNetworkMask and SharingNetworkNumberEnd. Additionally to ensure wireless is shared correctly also add SharingNetworkNumberStart option to AirPort. After editing my file now looks like this:

<pre><code class="xml">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
&lt;plist version="1.0"&gt;
&lt;dict&gt;
  &lt;key&gt;NAT&lt;/key&gt;
  &lt;dict&gt;
    &lt;key&gt;AirPort&lt;/key&gt;
    &lt;dict&gt;
      &lt;key&gt;40BitEncrypt&lt;/key&gt;
      &lt;integer&gt;1&lt;/integer&gt;
      &lt;key&gt;Channel&lt;/key&gt;
      &lt;integer&gt;0&lt;/integer&gt;
      &lt;key&gt;Enabled&lt;/key&gt;
      &lt;integer&gt;0&lt;/integer&gt;
      &lt;key&gt;NetworkName&lt;/key&gt;
      &lt;string&gt;LeanneNorthrop&lt;/string&gt;
      &lt;key&gt;NetworkPassword&lt;/key&gt;
      &lt;data&gt;
      &lt;/data&gt;
      &lt;key&gt;SharingNetworkNumberStart&lt;/key&gt;
      &lt;string&gt;192.168.1.1&lt;/string&gt;
    &lt;/dict&gt;
    &lt;key&gt;Enabled&lt;/key&gt;
    &lt;integer&gt;1&lt;/integer&gt;
    &lt;key&gt;NatPortMapDisabled&lt;/key&gt;
    &lt;false/&gt;
    &lt;key&gt;PrimaryInterface&lt;/key&gt;
    &lt;dict&gt;
      &lt;key&gt;Device&lt;/key&gt;
      &lt;string&gt;en0&lt;/string&gt;
      &lt;key&gt;Enabled&lt;/key&gt;
      &lt;integer&gt;0&lt;/integer&gt;
      &lt;key&gt;HardwareKey&lt;/key&gt;
      &lt;string&gt;&lt;/string&gt;
      &lt;key&gt;PrimaryUserReadable&lt;/key&gt;
      &lt;string&gt;Wi-Fi&lt;/string&gt;
    &lt;/dict&gt;
    &lt;key&gt;PrimaryService&lt;/key&gt;
    &lt;string&gt;4031C975-9F19-400A-B2B7-ECD102508D02&lt;/string&gt;
    &lt;key&gt;SharingDevices&lt;/key&gt;
    &lt;array&gt;
      &lt;string&gt;en5&lt;/string&gt;
    &lt;/array&gt;
    &lt;key&gt;SharingNetworkMask&lt;/key&gt;
    &lt;string&gt;255.255.255.0&lt;/string&gt;
    &lt;key&gt;SharingNetworkNumberEnd&lt;/key&gt;
    &lt;string&gt;192.168.1.50&lt;/string&gt;
    &lt;key&gt;SharingNetworkNumberStart&lt;/key&gt;
    &lt;string&gt;192.168.1.0&lt;/string&gt;
  &lt;/dict&gt;
&lt;/dict&gt;
</plist>
</code></pre>

I copied back the file and rebooted for good measure.

```
sudo cp ~/com.apple.nat.plist /Library/Preferences/SystemConfiguration/com.apple.nat.plist
```

So that was one end configured but my Pi's still couldn't ping the big outside world. I needed to configure a gateway and a network route on each Pi so I updated each /etc/systemd/network/eth0 file. Because the default network software of a fresh Arch Linux Arm install uses systemd-networkd to manage the network interfaces to enable internet sharing I also had to enable packet forwarding by using the IPForward flag. As I had statically assigned IP addresses I also required to add a Route section. See [Arch Linux Systemd-network wiki](https://wiki.archlinux.org/index.php/Systemd-networkd) for further details. So now my /etc/systemd/network/eth0 file looks like this:


<pre><code>
[Match]
Name=eth0

[Network]
Address=192.168.1.9/24
Gateway=192.168.1.1
DNS=192.168.1.1
DNS=8.8.8.8
DNS=8.8.4.4
IPForward=kernel

[Route]
Gateway=192.168.1.1
</code></pre>

After rebooting the Pis I could finally ping news.bbc.co.uk! Running `pacman -Syu` ensured each Pi had the latest Arch Linux packages.