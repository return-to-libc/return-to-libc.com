+++
title = "Install HP AMS on Ubuntu Focal 20.04 LTS"
date = "2020-10-03T19:30:00+02:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["hp", "ams", "ubuntu"]
keywords = ["hp", "ams", "install", "ubuntu"]
description = "How to install HP AMS on Ubuntu Focal 20.04 LTS"
showFullContent = false
+++
I have an HP ML310e gen8 that I bought on Ebay a few months ago. I love it!

But this is a bit old server, which already has a few years on the market and that HP no longer provides support. So I'm finding some problems.

In this case, I wanted to install [HP AMS](https://support.hpe.com/hpesc/public/docDisplay?docId=a00045520en_us&docLocale=en_US) to improve the ILO more information about the system.

Browsing through the [software repository](https://downloads.linux.hpe.com/SDR/project/mcp/), I found that Focal no longer supported hp-ams, but the previous version Bionic did.

To install it is very simple, first we create the file `/etc/apt/sources.list.d/mcp.list`

  > deb http://downloads.linux.hpe.com/SDR/repo/mcp bionic/10.80 non-free

Update the repositories, and install it with `apt install hp-ams`
