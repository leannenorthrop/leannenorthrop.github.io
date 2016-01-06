---
layout: post
title: Bramble ~ Kubernetes 1.1.2, Docker 1.9.1 on Arch Linux Arm
tags: [raspberry-pi, bramble, arm, archlinux, docker, kubernetes]
blogpost: true
---
So I spent the morning completing my installation of Docker 1.9.1 on my remaining Pi nodes and although I wished I had run up Ansible to handle the install, or better installed on my Arch Linux Arm image before writing out the SD cards, it turns out to be useful as I was able to read up on various topics whilst waiting for packages to download.

Just after receiving my bramble cluster for Christmas a little Googling brought [this interesting How To for Kubernetes on Docker on Pi by Arjen Wassink](http://blog.kubernetes.io/2015/12/creating-raspberry-pi-cluster-running.html) to my attention. Being optimistic I thought it would be a straight-forward thing to set up and get running but if you've read my [post on installing Docker 1.9.1 on Arch Linux Arm]({{site.baseurl}}/2016/01/04/docker-on-arm-arch-linux.html) you'll see that it took a little while to figure out how to get Docker running.

Kubernetes drew my attention as my experience of automating installs via Capistrano taught me the value of automation for deploying applications and I couldn't see the point of handling Docker on a node by node basis. Yes I've seen Swarm for Docker but reading through [this article from O'Reilly](http://radar.oreilly.com/2015/10/swarm-v-fleet-v-kubernetes-v-mesos.html) swayed me to give Kubernetes a go.

Arjen Wassink describes in his post how to run Kubernetes inside Docker containers and has kindly shared his Kubernetes [install scripts and files](https://github.com/awassink/k8s-on-rpi) on GitHub which ensure the services are brought up in the correct order. I've ended up forking his files [here](https://github.com/leannenorthrop/k8s-on-rpi) to fix a couple of issues with the Docker daemon. There is [another project on GitHub](https://github.com/luxas/kubernetes-on-arm) providing SD images with all components installed and working but I enjoy the experience of learning how to install and build components myself.

The Kubernetes on Docker on Pi article adequately describes the install process, however for those in a hurry you can:

1. Check that docker can run images on each node
2. `git clone https://github.com/leannenorthrop/k8s-on-rpi` to each node
3. Update rootfs/etc/kubernetes/k8s.conf to your own requirements (on each node)
4. On designated master node run install-k8s-master.sh, which may take a little while to download the images.
5. On each worker node run install-k8s-worker.sh
6. On master node `git clone https://github.com/luxas/kubernetes-on-arm.git` to grab the ARM Kubernetes binaries

To check the master node install:

1. `docker -H unix:///var/run/docker-bootstrap.sock ps` to ensure the flannel and etcd services are running
2. `docker ps` to ensure the Kubernetes controller, scheduler, apiserver, kubelet and proxy are running
3. If all your workers are up and running then on master node running `kubectl get nodes` should list each of your workers e.g.

<pre><code class="bash hljs">[alarm@master-baker]$ <span class="hljs-string">kubectl get nodes</span>
NAME            LABELS                                 STATUS
127.0.0.1       kubernetes.io/hostname=127.0.0.1       Ready
blueberry-pi    kubernetes.io/hostname=blueberry-pi    Ready
custard-pi      kubernetes.io/hostname=custard-pi      Ready
gooseberry-pi   kubernetes.io/hostname=gooseberry-pi   Ready
peach-pi        kubernetes.io/hostname=peach-pi        Ready
strawberry-pi   kubernetes.io/hostname=strawberry-pi   Ready</code></pre>

Arjen Wassink's scripts are a great help but I mostly had to execute each of service/docker commands manually to get the services installed and up and running. If I have to do it again I may break the install scripts into chunks so that the process is more step-wise.

So with more patience I finally have my Kubernetes/Docker cluster up and running and ready to start building some docker images for Scala/Java/Akka/Spark. I think though I will need some time to work through [Kubernetes User Guide](https://github.com/kubernetes/kubernetes/blob/release-1.1/docs/user-guide/README.md).