---
layout: post
title:  "Installing OpenWrt on an ASUS RT-AX53U"
date:   2023-02-12 14:55:00 +1100
category: projects
---
<h3>Background</h3>

For many years I have used consumer network routers that I have flashed with OpenWrt firmware.
Honestly most of the time It's a small project setting up the devices how I've wanted them and then 
basically left them alone. Over the years I have used OpenWrt on the following devices:

<ul>
<li>NetGear WNDR3700 (alive on another network)</li>
<li>Western Digital My Net N750 (dead)</li>
<li>Linksys WRT1900AC (v1) (retired)</li>
<li>ASUS RT-AX53U (in service)</li>
</ul>

I have seen comments to the effect that the Linksys E8450 / Belkin RT3200 are currently the best available 
WiFi 6 routers that are OpenWrt-supported, but I am not aware of any retailers in Australia for these 
devices, and I'm not yet willing to try to import any and deal with shipping costs and getting AU power 
bricks. Occasionally the RT-AX53U goes on special on Amazon AU for under $100, so so far I've bought two. 
Originally the second was intended to be a backup device should I have had any issues with the first 
device through normal use (unlikely) or when configuring or upgrading firmware (the typical cause of me 
bricking routers).

<h3>Installation</h3>

The OpenWrt Table of Hardware has a fairly straight-forward <a href="https://openwrt.org/toh/asus/rt-ax53u">
guide</a> for installing OpenWrt on the RT-AX53U. I followed the process for installation with mtd-write 
without incident. I would recommend using the <a href="https://firmware-selector.openwrt.org/">
firmware selector</a> in order to determine which factory image to install.

There have been a few issues with the configuration that has shipped with the various OpenWrt firmwares 
for the RT-AX52U:
<ul>
<li>On older firmware I had issues connecting to the internet. This I resolved by backing up config from 
my old WRT1900AC and restoring onto the RT-AX53U. I do not believe that this is an issue in the latest 
firmware release.</li> 
<li>More recently when I had to unbrick my second RT-AX53U I had issues with getting the 5GHz radio 
working with factory settings. Looking at the differences in "/etc/config/wireless" I had to manually add 
"option band '5g'" and "option channel '153'" to "config wifi-device radio1".</li>
</ul>

If required follow other guides for setting up the wireless access points. Please note that some devices 
do not or will not support WPA3. I have run into this with some flavours of Raspberry Pi, which I have not 
been motivated enough to try to solve yet.

<h3>Bricking</h3>

This weekend I bricked the second RT-AX53U when trying to configure it as a dumb AP for the purposes of it 
being a mesh node. How I did this was that I had got to the point of there only being a LAN interface and 
I had set it to DHCP mode in a (very incorrect) attempt to resolve the inability to download packages. I 
found myself unable to connect to the device any more to change the configuration, so then I set about 
trying to unbrick it.

<h3>Unbricking</h3>

My initial attempts to unbrick the router were to attempt to follow the installation with TFTP 
instructions. I have used a similar process to previously unbrick the WRT1900AC on multiple occasions. 
This involved opening up the router and connecting to the JTAG using a PL2303 (Prolific USB to Serial) 
cable. Unlike the WRT1900AC there are no pins on the RT-AX53U JTAG which makes it fiddly as. Windows 10/11 ships with deliberately broken drivers for the PL2303. I was able to find 
a copy of an older working driver on a <a href="http://support.dlink.com.au/Download/download.aspx?product=DGS-3630-28PC">
D-Link support</a> page. Annoyingly updating the driver requires a restart, and whenever you plug in 
the cable it defaults to the newer nonfunctional driver. Also running the tFTP server software for 
downloading the firmware image to the router has connections blocked by windows firewall. After I spend 
quite some time hitting these issues in various combinations I was able to get the router to download the 
firmware successfully only for the flashing to fail.

Next I attempted to follow the <a href="https://www.asus.com/support/FAQ/1000814/">ASUS Rescue Mode</a> 
instructions. I got through the whole process only for the final status to indicate that there was 
an error flashing the firmware. Later with the JTAG connected I saw that on booting the router detected 
that there was an issue with the firmware in the flash and that it then restored from the other partition. 
Then I was back to factory firmware running but with a password I could not remember.

First I attempted to restore to factory settings by following the 
<a href="https://www.asus.com/support/FAQ/1044653/">reset to factory settings</a> instructions. This 
did not work. Then I located these <a href="https://www.asus.com/support/FAQ/1039077/">Hard Factory 
Reset</a> instructions which did work. Then I successfully followed the instructions to install OpenWrt.