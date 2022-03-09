<h2>Prepare the SD card</h2>



The Raspberry Pi expects a boot loader and operating system on the SD card. In order to use the Pi, you must first install the operating system. For this implementation we’ll use Raspberry Pi OS Lite (a Debian build optimized for the Pi hardware). 

<img src="https://gist.github.com/mr-canoehead/09fe6f467f0d080862afd8802045e302/raw/43be712d4fef3918db22225f64500251ba387ab3/warn.png" alt="warning" width="30"> <i>Note: each new release of Raspberry Pi OS introduces changes which may cause problems with the VPN Client Gateway installation procedure and could possibly break one or more scripts. This install guide has been updated and tested to work with the recent Raspberry Pi OS Lite (2021-01-11) release.</i>

After downloading the OS image, write it to the SD card. I recommend using the [Raspberry Pi Imager](https://www.raspberrypi.org/blog/raspberry-pi-imager-imaging-utility/) utility for this task.

<h2>Perform initial setup</h2>

Now that the SD card has been prepared, we'll boot up the Pi and perform the initial OS configuration:

1. connect the HDMI cable, Ethernet cable, and USB keyboard + mouse to the Pi.

2. insert the SD card into the slot on the Pi.

3. connect the 5v power supply.

*Note: the HDMI cable must be connected before booting the Pi, otherwise the display will remain
blank until the next reboot.

To start the Raspbian configuration utility, open a terminal window and run the command:

<code>sudo raspi-config</code>

Perform the following actions:

+ System Options
	+ S3 Password
	+ S4 Hostname
	+ S5 Boot / Auto Login &#x279e; B1 Console (text console, requiring user to log in)

+ Interface Options &#x279e; P2 SSH (enable the ssh server)

+ Performance Options &#x279e; P2 GPU Memory (set to 16 MB)

+ Localisation Options
	+ L1 Locale
	+ L2 Timezone
	+ L3 Keyboard
	+ L4 WLAN Country

+ Advanced Options &#x279e; A1 Expand Filesystem

Select <b>Finish &#x279e; Yes</b> to complete the initial configuration and reboot the Pi.

<h2>Configure network</h2>

At the login prompt, log in using the username ‘pi’ and the password you entered in the setup utility. Use the <b>ip</b> command to view the Pi's IP address:

<code>ip addr</code>

You can now log in to your Pi via ssh from your desktop PC, e.g.:

<code>ssh -l pi 10.1.2.50</code>

<h3>Assign a static IP address</h3>
Edit the file /etc/dhcpcd.conf:

<code>sudo nano /etc/dhcpcd.conf</code>

Here is an example /etc/dhcpcd.conf with static IP:
```
interface eth0
static ip_address=10.1.2.50/24	
static routers=10.1.2.1
static domain_name_servers=1.1.1.1 1.0.0.1
```

<i>*Note: modify the settings to match your network. You should use your VPN provider's DNS servers for the domain_name_servers setting.</i>

<h2>Update Raspbian</h2>
Run the following commands to update the Raspbian OS and supporting software to the latest release:

<code>sudo apt update</code>

<code>sudo apt upgrade -y</code>

The upgrade operation may take several minutes to complete

<h2>Reboot the server</h2>

<code>sudo reboot</code>

After the Pi has rebooted, verify that the static IP address is being used.

<img src="https://gist.github.com/mr-canoehead/09fe6f467f0d080862afd8802045e302/raw/43be712d4fef3918db22225f64500251ba387ab3/warn.png" alt="warning" width="30"> <i>Note: remember to reserve the Pi's IP address in your home network router to prevent conflicts.</i>

<hr>

[Proceed to Part 2](../part2/README.md)
