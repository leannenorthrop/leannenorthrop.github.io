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

<img class="img-responsive center-block" alt="Parts for Raspberry Pi Cluster" src="{{site.baseurl}}/images/brambleparts.JPG">

I started by writing out the Arch Linux Arm image I [prepared a couple of days ago]({{site.baseurl}}/2015/12/28/bramble-prep.html) to each of the SD cards which turned out to be quicker than I expected and completed in under an hour.

Hooking the Pis up to power and network switch was straight forward and with intrepidation I powered them up. A satisfying glow of twinkling red lights appeared. As we don't have a Christmas tree this year this was the closest we will get to christmas lights.

The next issue was how to 'talk' to each Pi as none of them have a wireless connector and the switch wasn't connected to our broadband router. Each had a self assigned address and it wasn't possible to quickly find out the IP address. So I decided to have that warm fuzzy feeling of knowing what host is on what IP address and go down the route of static IP addresses for each node.

## Setting Up Static IP on Arch Linux Arm

It took a little while to figure out how to assign a static IP address in Arch Linux as there's a few different sets of software that manages network connections which if all enabled at the same time conflict with each other. Eventually I figured out that with a fresh install of Arch Linux having touched nothing else all I needed to do was update the configuration in the /etc/systemd/network/eth0.network file to include a Network section with the address and and a gateway. I decided to use the 192.168.1.x range of addresses so the /etc/systemd/network/eth0.network file on each Pi looks like this (except the address line differed on each Pi e.g. Address=192.168.1.2/24, Address=192.168.1.3/24, Address=192.168.1.4/24 for the first 3 nodes.):

<pre><code class="hljs cpp">[Match]
Name=eth0

[Network]
Address=<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.2</span>/<span class="hljs-number">24</span>
Gateway=<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>
</code></pre>

 The gateway address is the address I wanted my Mac to have (more on that below). I also updated the [/etc/hostname file](https://wiki.archlinux.org/index.php/Beginners'_guide#Hostname) to assign names e.g. strawberry-pi, custard-pi etc. and rebooted each Pi node.

### Setting Up Static IP on OSX

I was hoping that simply connecting my Mac to the network switch would be sufficient but I needed to tinker a bit. So I opened up System Preferences, Network settings and using the + button at the bottom of the list added in the Thunderbolt Ethernet interface and named it Pi Cluster. To enable communication I had to set the Configure IP4 to Manually and pop in my chosen IP address in the 192.168.1.x range of 192.168.1.1, setting 255.255.255.0 in Subnet Mask is helpful as well as using the Advanced button to add in Google's DNS servers to the DNS tab.

<img class="img-responsive center-block" alt="OSX Manual Ethernet Connection Settings" src="{{site.baseurl}}/images/clusterosxnetworksettings.png">

With that I could happily ssh into each Pi but the manual setting caused me to lose my Internet connection as Safari etc was attempting to access the internet via my wired switch rather than via wireless router. Thankfully it's simple to fix via the System Preferences Network tab by using the Set Service Order to move the wireless interface above the wired interface:

<img class="img-responsive center-block" alt="OSX Set Service Order Menu Option" src="{{site.baseurl}}/images/clusterosxserviceorder.png">

Service order changed to:

<img class="img-responsive center-block" alt="OSX Set Service Order Menu Option" src="{{site.baseurl}}/images/osxserviceorder.png">

So now with internet restored on my Mac I quickly realised I couldn't use pacman to install packages on each Raspberry Pi without setting up internet sharing on my Mac. Again I thought it would be a straight forward matter of simply turning on network sharing in the System Preferences Sharing tab but no.....

## OSX Sharing Internet to Static IPs

As it turns out enabling Internet Sharing on OSX fires up a DHCP server which assigns devices IP address in the range of 192.168.2.x. What a pain! As there is no easy dialog to change this IP address range it required a little tinkering around in the terminal window to update the /Library/Preferences/SystemConfiguration/com.apple.nat.plist file. I created a backup and copied the file to my home directory and edited it with Sublime Text.

```
sudo cp /Library/Preferences/SystemConfiguration/com.apple.nat.plist /Library/Preferences/SystemConfiguration/com.apple.nat.backup
sudo cp /Library/Preferences/SystemConfiguration/com.apple.nat.plist ~
```

To assign the network address range you need to add SharingNetworkNumberStart settings but for El Capitan you also need to set SharingNetworkMask and SharingNetworkNumberEnd. Additionally to ensure wireless is shared correctly also add SharingNetworkNumberStart option to AirPort. After editing my file now looks like this:

<pre><code class="xml hljs"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-doctype">&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">plist</span> <span class="hljs-attribute">version</span>=<span class="hljs-value">"1.0"</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dict</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>NAT<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">dict</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>AirPort<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dict</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>40BitEncrypt<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">integer</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">integer</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>Channel<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">integer</span>&gt;</span>0<span class="hljs-tag">&lt;/<span class="hljs-title">integer</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>Enabled<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">integer</span>&gt;</span>0<span class="hljs-tag">&lt;/<span class="hljs-title">integer</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>NetworkName<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>LeanneNorthrop<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>NetworkPassword<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">data</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">data</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>SharingNetworkNumberStart<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>192.168.1.1<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dict</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>Enabled<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">integer</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">integer</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>NatPortMapDisabled<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">false</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>PrimaryInterface<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dict</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>Device<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>en0<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>Enabled<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">integer</span>&gt;</span>0<span class="hljs-tag">&lt;/<span class="hljs-title">integer</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>HardwareKey<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>PrimaryUserReadable<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>Wi-Fi<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dict</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>PrimaryService<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>4031C975-9F19-400A-B2B7-ECD102508D02<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>SharingDevices<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">array</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>en5<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">array</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>SharingNetworkMask<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>255.255.255.0<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>SharingNetworkNumberEnd<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>192.168.1.50<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">key</span>&gt;</span>SharingNetworkNumberStart<span class="hljs-tag">&lt;/<span class="hljs-title">key</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">string</span>&gt;</span>192.168.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">string</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">dict</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dict</span>&gt;</span>
</code></pre>

I copied back the file and rebooted for good measure.

```
sudo cp ~/com.apple.nat.plist /Library/Preferences/SystemConfiguration/com.apple.nat.plist
```

So that was one end configured but my Pi's still couldn't ping the big outside world. I needed to configure a gateway and a network route on each Pi so I updated each /etc/systemd/network/eth0 file. Because the default network software of a fresh Arch Linux Arm install uses systemd-networkd to manage the network interfaces to enable internet sharing I also had to enable packet forwarding by using the IPForward flag. As I had statically assigned IP addresses I also required to add a Route section. See [Arch Linux Systemd-network wiki](https://wiki.archlinux.org/index.php/Systemd-networkd) for further details. So now my /etc/systemd/network/eth0 file looks like this:


<pre><code class="hljs cpp">[Match]
Name=eth0

[Network]
Address=<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.9</span>/<span class="hljs-number">24</span>
Gateway=<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>
DNS=<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>
DNS=<span class="hljs-number">8.8</span><span class="hljs-number">.8</span><span class="hljs-number">.8</span>
DNS=<span class="hljs-number">8.8</span><span class="hljs-number">.4</span><span class="hljs-number">.4</span>
IPForward=kernel

[Route]
Gateway=<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>
</code></pre>

After rebooting the Pis I could finally ping news.bbc.co.uk! Running `pacman -Syu` ensured each Pi had the latest Arch Linux packages.