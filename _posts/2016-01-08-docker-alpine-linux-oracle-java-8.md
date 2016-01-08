---
layout: post
title: Bramble ~ Docker Image for Oracle Java 8 on Alpine Linux 3.2
tags: [raspberry-pi, bramble, arm, docker, alpine, java]
blogpost: true
---
Now that I have the bramble up and running with Docker 1.9.1 and Kubernetes of course the next question was what to run on it! Pampered by the huge range of package repositories that have become available in recent years, each with a wealth of software to hand at the type of a single command, I'm finding that working on the bramble hasn't been all that easy. Was it possible to find a docker image for Oracle Java 8 that runs on a Pi....? I guess I wasn't really surprised to find it wasn't but I thought it was about time I contributed back to the web and so there is now ~ well [the Dockerfile at least](https://github.com/leannenorthrop/rpi-alpine-oracle-jre/blob/master/Dockerfile).

Building my first docker images has been interesting journey. I was smitten by the prospect of having tiny images so my SD cards don't fill up and Alpine Linux at first jumps out to be a good choice. I'm a little concerned about the [report that Alpine runs from memory](https://www.reddit.com/r/linux/comments/3mqqtx/alpine_linux_why_no_one_is_using_it/) as I will eventually need every ounce of memory I can get but as I'm exploring the terrority it's good for now.

My first and really only issue has been the fact that Alpine Linux [doesn't support Java 8 out of the box](https://github.com/gliderlabs/docker-alpine/issues/11) due to the fact that Alpine uses musl libc rather than glibc. It's a bit surprising given the range of applications built on glibc but I'm sure they have their reasons I've yet to discover. 

So it was a bit of a downer but thankfully reading through the post a non-main repository based APK was kindly provided Andy Shinn and coming across [Gary Wisniewski's docker file for Oracle Java on Alpine Linux](https://github.com/garywiz/chaperone-docker/blob/master/alpinejava/setup/install.sh) I thought heh, no worries. Of course I was too fast for my own good as it was obvious immediately after starting up the resultant image that something was amiss and it didn't take long to figure out that the APK was for x86 archictecture. Argh! 

Given that it's been years since I've used gcc, make etc. I wasn't relishing the prospect at building it from scratch but turning to Andy's work it was fairly simple to build on master-baker (master Raspberry Pi node), create an Alpine Linux abuild image and generate the APK files. It took an hour or so to build glibc and a couple more to figure out how to build the APK correctly. The fun began though figuring out how to get java to actually run.

I spent hours trying to get the right combination of LD_LIBRARY_PATH, libraries, permissions correct. Infuriatingly I even managed to have a working partial image and then lost the history by accidentally shutting it down. I spent another 3 hours getting back to that point aided by the interuption of my partner which gave me a long enough break to see the problem with fresh eyes (thanks Andy :-) ). But it was a good learning experience and I eventually got ldconfig to work as well and was literally overjoyed when I had a reproducable Docker image with working java. 

The only fly in the ointment has been the fact that Oracle Java seems to also require libgcc in addition to glibc. Not wanting to inflate the image any further I opted to install the current Arch Linux Arm libgcc libraries rather than pull the relevant APK. I'm sure that if anything blows up it will be due to this.

Based on the execellent work by the Hypriot team that supplies the [Alpine Linux Scratch for Raspberry Pi the Dockerfile](https://github.com/hypriot/rpi-alpine-scratch) I came up with this:

<pre><code class="hljs">FROM hypriot/rpi-alpine-scratch

MAINTAINER Leanne Northrop &amp;lt;lavender.flowerdew@gmail.com&amp;gt;

LABEL Description="This image is used as base image for my explorations of running JAVA/Node applications on a Raspberry Pi 2 cluster." Version="0.1"

# Set environment
ENV JAVA_VERSION=8 \
    JAVA_UPDATE=65 \
    JAVA_BUILD=17 \
    JAVA_HOME="/opt/jdk" \ 
    PATH=$PATH:${PATH}:/opt/jdk/bin

# Download and install glibc
RUN apk update &amp;&amp; \
  apk upgrade &amp;&amp; \
  apk add bash wget ca-certificates &amp;&amp; \
  wget https://github.com/leannenorthrop/alpine-pkg-glibc/releases/download/glibc-2.22-r1-armhf-beta/glibc-2.22-r1.apk &amp;&amp; \
  wget https://github.com/leannenorthrop/alpine-pkg-glibc/releases/download/glibc-2.22-r1-armhf-beta/glibc-bin-2.22-r1.apk &amp;&amp; \
  wget https://github.com/leannenorthrop/alpine-pkg-glibc/releases/download/glibc-2.22-r1-armhf-beta/libgcc_s.so &amp;&amp; \
  wget https://github.com/leannenorthrop/alpine-pkg-glibc/releases/download/glibc-2.22-r1-armhf-beta/libgcc_s.so.1 &amp;&amp; \
  apk add --allow-untrusted glibc-2.22-r1.apk &amp;&amp; \
  apk add --allow-untrusted glibc-bin-2.22-r1.apk &amp;&amp; \
  mv libgcc* /lib &amp;&amp; \
  chmod a+x /lib/libgcc_s.so* &amp;&amp; \
  cp /usr/glibc-compat/lib/ld-linux-armhf.so.3 /lib &amp;&amp; \
  wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-arm32-vfp-hflt.tar.gz" &amp;&amp; \
  tar -xzf jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-arm32-vfp-hflt.tar.gz &amp;&amp; \
  echo "" &amp;gt; /etc/nsswitch.conf &amp;&amp; \ 
  mkdir /opt &amp;&amp; \
  mv jdk1.${JAVA_VERSION}.0_${JAVA_UPDATE} /opt/jdk-${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD} &amp;&amp; \
  ln -s /opt/jdk-${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD} /opt/jdk &amp;&amp; \
  ln -s /opt/jdk/jre/bin/java /usr/bin/java &amp;&amp; \
  /usr/glibc-compat/sbin/ldconfig /lib /usr/glibc-compat/lib /opt/jdk/lib /opt/jdk/jre/lib /opt/jdk/jre/lib/arm /opt/jdk/jre/lib/arm/jli &amp;&amp; \
  echo "hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4" &amp;gt;&amp;gt; /etc/nsswitch.conf &amp;&amp; \
  rm -f glibc-*.apk jdk*.tar.gz /opt/jdk/src.zip &amp;&amp; \
  rm -rf $JAVA_HOME/jre/bin/jjs \
       $JAVA_HOME/jre/bin/keytool \
       $JAVA_HOME/jre/bin/orbd \
       $JAVA_HOME/jre/bin/pack200 \
       $JAVA_HOME/jre/bin/policytool \
       $JAVA_HOME/jre/bin/rmid \
       $JAVA_HOME/jre/bin/rmiregistry \
       $JAVA_HOME/jre/bin/servertool \
       $JAVA_HOME/jre/bin/tnameserv \
       $JAVA_HOME/jre/bin/unpack200 
  apk del wget ca-certificates &amp;&amp; \
  rm -rf /var/cache/apk/* 

# Define default command.
CMD ["bash"]</code></pre>

I'm really sure it can be pared right down but I felt that should be done on an application by application basis. Hopefully when I get time I'll update the [alpine-pkg-glibc](https://github.com/leannenorthrop/alpine-pkg-glibc) repo with a reproducible build of glibc on arm with APK file creation but I'm really curious to see what java apps I can get running. Spark? AKKA?

Really helpful links that supported this adventure and a debt of gratitude for helping me to get this working:

* [Original Inspiring Dockerfile](https://hub.docker.com/r/frolvlad/alpine-oraclejdk8/~/dockerfile/)
* [Alpine Glibc Builder Dockerfile for x86](https://github.com/jeanblanchard/docker-alpine-glibc/blob/master/Dockerfile)
* [Helpful hint for APK building](https://github.com/Boggart/alpine-pkg-glibc-32bit/blob/master/APKBUILD)
* [Interesting comments on paring down](https://gist.github.com/rhuss/6d3c0c687fb4ec39c3e4)
* [Great start building Java Image for Docker](https://developer.atlassian.com/blog/2015/08/minimal-java-docker-containers/)
* [Docker Build Ref](https://docs.docker.com/engine/reference/builder/)
* [Tutorial for Using Docker Images](http://containertutorials.com/images.html)
* [Docker docs](https://docs.docker.com/engine/userguide/basics/)
