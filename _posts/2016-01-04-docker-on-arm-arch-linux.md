---
layout: post
title: Bramble ~ Docker 1.9.1 on Arch Linux Arm, Patience Required
tags: [raspberry-pi, bramble, arm, archlinux, docker]
blogpost: true
---
So one of my aims with the bramble is to learn a multitude of things like, Spark, Akka, NodeJS, etc. however previous to now I have only worked with non-virtualised machines for deploying distributed applications using Capistrano. I didn't want to fork out on Amazon services to learn this technology and purchasing full nodes was a bit expensive so I'm thrilled to now have my own cluster to play with. 

So first on the list to learn is Docker, specifically the light-weight engine for hosting container 'images'. In these security conscious times it's probably worthwhile to read [this](https://opensource.com/business/14/7/docker-security-selinux) before going ahead with Docker but since my bramble is a wired only affair with no internet access unless I enable it I feel safe to continue. 

As my bramble is running Arch Linux Arm everything is at latest edition which makes deploying applications, well, interesting. At the time of writing this Docker in the Arch Linux Arm package repository is 1.9.1 and was compiled with Go 1.5. It's installed using the arch linux package manager, pacman like this:

<pre><code class="bash hljs">pacman -S docker polkit</code></pre>

Enable the service using `systemctl enable docker.service`. I discovered the polkit package is required after installing just docker and it failed to run. Even with this dependency install I was struggling to get even `docker info` running.

Hunting around I came across [this bug report](https://github.com/docker/docker/issues/18125) which indicates an issue of underlaying Go language on ARM. The true fix is to compile Docker on ARM against Go 1.4 however I'm in a hurry and someone has very kindly provided a pre-built binary. Following the suggested work around in the bug report I replaced the pacman installed docker with the new binary:

<pre><code class="bash hljs">curl -L <span class="hljs-string">'https://github.com/armhf-docker-library/binaries/blob/master/docker-1.9.1?raw=true'</span> | sudo tee /usr/bin/docker &gt;/dev/null &amp;&amp; sudo chmod +x /usr/bin/docker</code></pre>

After rebooting this works a treat and now running `docker images` and other commands work nicely.

Next up was trying to run my first image. This too threw up errors and another day of trying to get to the bottom of things. I eventually found [this post](http://archlinuxarm.org/forum/viewtopic.php?t=9337&p=48702) and [this bug report](https://github.com/docker/docker/issues/16256) which indicated the latest systemd version was causing an issue with cgroups. The magic fix in this case was to use Docker's `--exec-opt` to set the cgroupdriver e.g.:

<pre><code class="bash hljs">docker <span class="hljs-operator">-d</span> --exec-opt native.cgroupdriver=cgroupfs</code></pre>

With this in place it was now possible to run up a busybox image on master-baker (my Raspberry Pi master node).

Lastly I was tired of using sudo all the time to execute docker commands so reading through [this](http://askubuntu.com/questions/477551/how-can-i-use-docker-without-sudo) I executed `sudo gpasswd -a ${USER} docker`. 

Patience was definitely required to get this running. I initially thought it would only take a weekend to get a Raspberry Pi cluster up and running with Kubernetes on Docker but the OSX networking 'features' and docker issues have slowed me down considerably. However I'm pleased to say I finally got Kubernetes running succesfully on two nodes yesterday and I hope to post soon on that.