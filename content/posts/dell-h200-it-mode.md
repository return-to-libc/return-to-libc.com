+++
title = "Flash Dell Perc H200 IT mode"
date = "2023-05-21T23:00:00+02:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["dell", "h200", "flash"]
keywords = ["dell", "h200", "flash", "it", "mode"]
description = "How to Flash Dell Perc H200 and not die trying"
showFullContent = false
+++

I'm building a small rack, and right now I'm finishing my 2U backup server (I have to show you a picture when everything is ready)... I got a Dell Perc H200 controller and my idea was to switch it to IT mode to be able to do the raid directly on the OS.

And what an odyssey!

After reading different tutorials, I realised that either the steps were missing, or they were in the wrong order, or it didn't work in UEFI mode (hello 21st century).

Here are the files and steps to flash your Dell Perc H200 card and leave it in IT mode with UEFI support.

- Download all the files from [here](https://return-to-libc.com/downloads/LSI-9211-8i.zip).
- Prepare a USB with freedos bootable.
- Copy all the files to the USB. Overwriting is not needed.
- Boot into the UEFI console. This depends on your motherboard model. So, please, check the official documentation :)
- `map -b` and find your USB filesystem. In my case was FS5.
- `fsX:` to change to the USB fylesystem. Replace X with your value.
- `sas2flash.efi -listall` to find your Dell Perc H200 to flash the firmware. If you only have 1 card inserted (I recommend it), this should be 0.
- `sas2flash.efi -c X -list` Replace X with your value, in my case was 0. Write down the SAS address.

Now restart the computer and boot the freedos USB. In the DOS prompt `C:>`:

- `megarec.exe -writesbr 0 sbrempty.bin`
- `megarec.exe -cleanflash 0`

Restart and boot the UEFI shell again. In the UEFI shell:

- `fsX:` to change to the USB fylesystem. Replace X with your value.
- `sas2flash.efi -o -f 6GBPSAS.FW`

Restart and boot the UEFI shell again. In the UEFI shell:

- `fsX:` to change to the USB fylesystem. Replace X with your value.
- `sas2flash.efi -o -f 2118p7.bin`

Restart and boot the UEFI shell again. In the UEFI shell:

- `sas2flash_p19.efi -o -f 2118it.bin -b mptsas2.rom`
- `sas2flash_p19.efi -o -sasadd xxxxxxxxxxxxxxxx` where xxxxx is your SAS address you wrote down earlier, without the hyphens.
- `sas2flash_p19.efi -b x64sas2.rom`

Restart. Enjoy your Dell H200 in IT mode :)
