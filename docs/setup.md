# Before You Get Started.

### You'll need:

* A Raspberry Pi (I've only tested this on a B+), but it seems like it should work on others.
* A wifi dongle. Mine is an Edimax.
* Power (see below)
* An ethernet cable. Mine is "Go-faster Blue", but I heard a rumour colour doesn't effect BW. I remain skeptical.
* A microSD card. I'm just using one a 16GB card from maplins.
* A microSD card reader of some sort. My Mac doesn't have a port for microSD so I use an 'Integral MicroSD' reader, which is a USB reader that has a little slot for microSDs.
* Access to a wifi router. You'll need to be able to connect your Pi to it via Ethernet and be able to power your Pi at the same time.
* If you're on a mac, you will need `passlib` installed on your mac. We use it to do the password hashing when it changes the password for `pi`. Detailed below.

A note about power: when I first started out, I used an old nokia charger (UK wall plug --> micro USB) but it didn't work because it wasn't providing enough current to the Pi. I now successfully use an apple iPhone charger plug (which has a female USB) and a USB --> microUSB cable. Whatever you decide to use, check the spec of the plug part; it usually states it's output volts and amps in font size 2.5 somewhere. If you're shy on plug sockets, get a 10,000 MAh battery; you won't regret it.

### You won't need:

* Keyboard
* Mouse
* HDMI cable
* A monitor

*(Unless things go tits-up, in which case you might)*

This is all about automating the installation and configuration of your Pi. It's for people who might not want to remember all those magical incantations and learn-by-doing installations this time round. I'm imaging you searched for 'Get me to the point with a Pi where I can develop and install my App (goddamit)' and ended up here.

# Installing the Operating System.

### 1. Download the latest Raspbian Image. I'm using Jesse.

You can download from [the official raspbian download page](https://www.raspberrypi.org/downloads/raspbian/). It's about 4GB so it might take a while.

### 2. Download and Install PiBaker
PiBaker is an easy Mac Utility for writing images to SD cards. [AlternativesTo.com](http://alternativeto.net/software/applepi-baker/about/) lists the Win32 Disk Imager, which might work for you on Windows. Download PiBaker from [here](http://www.tweaking4all.com/software/macosx-software/macosx-apple-pi-baker/). Scroll down to the green boxes to find the actual download button.

### Write image to MicroSD.

![Image of the PiBaker UI](./images/piBakerWrite.png)

1. Insert your USB/SD card
2. Open PiBaker; you will probably have to provide your sudo password.
3. Check that there is something in the box labeled 'C' in the image above.
4. Use the file browser (labeled 'A' in the image above) to find your downloaded raspbian .img file.
5. Click 'Restore Backup' (labeled 'B').
6. If it all goes well, you won't see any errors and it'll provide a message saying you've successfully written your image and that it's automatically unmounted and ejected the disk. Good.

### Enable SSH

As of [November 2016](https://www.raspberrypi.org/documentation/remote-access/ssh/), SSH is disabled by default on the raspberry pi. You'll need it enabled to run any ansible playbook, so do the following:

* Take out your newly written micro SD card
* Re-insert it into your laptop
* In a file browser / finder window, find the device called `boot` on the left hand side.
* Copy `pi-setup/docs/preboot_files/ssh` to this `boot` device.
* That's it; you don't need to add any content or change permissions/ownership.

### Load and boot.

1. Stick the microUSB into your Raspberry Pi.
2. Connect the Pi to the router using the Ethernet cable.
3. Connect your Wireless dongle to the Pi.
4. Connect your Pi to the power.

# Locate and Test Connection.

### Find the Pi on your network.

1. Login to your router web admin page. Your laptop will need to be on the wifi access point provided by that router. In a browser, type in `192.168.0.1` or `192.168.0.2` in the url bar.

2. Have a browse around until you find a tab/page called something like 'Attached Devices', which should show you a list of the devices that are connected to your router, either wirelessly or wiredly (?).

3. In this list there should be a device called `raspberrypi`. Scribble down the IP v4 associated with your pi. It should be something like `192.168.0.xx` where `xx` is a number less than or equal to 255. From now on I'll use the syntax `<pi_ip>` to represent this IP address you've just found.

### Try SSHing into the Pi.

Open a terminal...

	$> ssh pi@<pi_ip>
	The authenticity of host '<pi_ip> (<pi_ip>)' can't be established.
	ECDSA key fingerprint is SHA256:<zzzzzz>.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '<pi_ip>' (ECDSA) to the list of known hosts.
	pi@<pi_ip>'s password:

	The programs included with the Debian GNU/Linux system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/\*/copyright.

	Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
	permitted by applicable law.
	Last login: Fri May 27 11:50:56 2016

	pi@raspberrypi:~ $

# Slight mod to your local machine (if Mac OSX)

If your control machine is running OSX, you'll need passlib installed. Run:

	$> sudo pip install passlib

Expect to see this error if you don't:

		TASK [pi-user : Creating user "pi" with admin access] **************************
		fatal: [<host>]:
		FAILED! => {"failed": true, "msg": "|
		password_hash requires the passlib python module to
		generate password hashes on Mac OS X/Darwin"}

# Passwordless SSH and Creating Keys.

We're going to create a key pair that allows you to ssh into your raspberry pi from your laptop without having to
use the password every time. It's optional; don't do it if you're already bored. The ansible script also needs some work to make this work seemlessly.

To create a new keypair, follow the following instructions. This assumes you're on a \*nix flavoured laptop/box.

1. Download or clone this repo.

2. Generate a key:

		$> ssh-keygen
		Generating public/private rsa key pair.
		Enter file in which to save the key (/Users/robrant/.ssh/id_rsa): /Users/robrant/.ssh/pikey.pem
		Enter passphrase (empty for no passphrase):
		Enter same passphrase again:
		Your identification has been saved in /Users/robrant/.ssh/pikey.pem.
		Your public key has been saved in /Users/robrant/.ssh/pikey.pem.pub.
		The key fingerprint is:
		SHA256:<my sha was in here>
		The key's randomart image is:

3. Change the name of the public key to `pikey.pub` and copy into the `pi-setup/public_keys` directory inside the downloaded/cloned version of this repository.

# Voodoo?

If by some voodoo, this all actually worked for you, then head to the [running.md](./running.md) to run the play. If it didn't, please [raise an issue](https://github.com/robrant/pi-setup/issues/new).
