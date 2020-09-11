+++
title = "Disable promiscuous mode in VMware Workstation"
date = "2020-09-11T18:00:00+02:00"
author = "elraro21"
authorTwitter = "elraro21" #do not include @
tags = ["network", "promiscuous", "vmware", "workstation"]
keywords = ["promiscuous", "vmware"]
description = "How to disable promiscuous mode in VMware Workstation."
showFullContent = false
+++

## How to disable promiscuous mode in VMware Workstation

Hello everyone.

It's been a long time since "hello world". I hope to be more active from now on...

To disable the promiscuous mode of some of the interfaces of our vm the first thing is to turn it off. Then we look for the associated VMX file. We open it with our favorite text editor and add the following (Replace %d with the interface number):

  > ethernet%d.noPromisc = "TRUE"

Now just turn your VM on again and you're done. Perfect for bridge mode networks.
