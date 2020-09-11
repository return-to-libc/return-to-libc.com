+++
title = "Podman rootless and SELinux, the easy way"
date = "2020-06-12T00:00:00+02:00"
author = "Elraro"
authorTwitter = "elraro21" #do not include @
cover = ""
tags = ["podman", "selinux"]
keywords = ["podman", "rootless", "selinux", "easy"]
description = "How to use Podman rootless containers with SELinux policies, and not die in the attempt"
showFullContent = false
draft = true
+++

## Podman rootless and SELinux, the easy way

Be honest, who installed Centos and the first thing you do is disable firewalld and SELinux? (hand raising)

I'm setting up a home server and taking advantage of the situation, I'm learning Centos 8 (SELinux enabled, crazy!). But I'm encountering problems with podman and SELinux policies, as I don't know how to create and apply them to containers.

Fortunately, I found a presentation by Lukas Vrabec that you can download [here](https://fosdem.org/2020/schedule/event/security_using_selinux_with_container_runtimes/attachments/slides/4154/export/events/attachments/security_using_selinux_with_container_runtimes/slides/4154/Fosdem_Using_SELinux_with_container_runtimes.pdf). In this presentation, he talks about a tool called [udica](https://github.com/containers/udica)

Installation is very simple:

```
$ sudo dnf install -y podman setools-console git container-selinux
$ git clone https://github.com/containers/udica
$ cd udica && sudo python3 ./setup.py install
```

In my case, I have problems creating the [Pihole](https://github.com/elraro21/my_containers) container, as it tries to expose port 443 and 53.

```
[...]
podman start -a pihole_pihole_1
ERRO[0003] error starting some container dependencies
ERRO[0003] "failed to expose ports via rootlessport: \"listen udp 0.0.0.0:53: bind: permission denied\\n\""
Error: unable to start container 32284e974ce506c16763b7b0d243a2190d219a4fae2dae7cffd95c30cae291e8: error starting some containers: internal libpod error
125
```


