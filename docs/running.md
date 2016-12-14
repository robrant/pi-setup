
## Usage
To use this Ansible playbook, first set some variables and then run the `./run`
script from the command line. Examples provided below.

## Hosts file

#### Configuring 1 Pi?

If this is the first time you've run this your default Raspbian OS has a hostname
of `raspberrypi`, which is the hostname provided in the `hosts` (inventory) file
within this playbook. Only change this if you know the IP or have changed the
hostname on the raspberry pi.

#### Configuring multiple Pis?

This playbook is written so that you can configure multiple Raspberry Pis in one
run of the playbook. You will need to provide the IPs of your new Pis in the
`hosts` file like so:

		[pi]
		192.168.0.3
		192.168.0.4
		192.168.0.5

To get away from using IPs, the hostname play will set the system `hostname`
variable of each host (in `/etc/hostname` on the pi) based on a prefix you
provide and the index position of the host within the `hosts` file `[pi]` group.
The variable that controls the prefix is `new_hostname_prefix`, which is set in
`hostname/defaults/main.yml` but can be overridden in the `playbook.yml` file.

Using the example above and given a `new_hostname_prefix` of `pibox`, it will
make the hosts file look like this:

		[pi]
		pibox0
		pibox1
		pibox2

and change the hostname (`/etc/hostname`) on the pis to the values above
(`pibox0`,`pibox1`,`pibox2`).

## Variables

### SSH

Having successfully setup key-based ssh, you can select to disable password-based login. In `playbook.yml`:

	vars:
	 	- password_login_permitted : ( "yes" | "no" )

I think these values need to be quoted so that ansible treats them as a string
and not boolean. **It is currently recommended that you set this to "yes" - it needs
some more testing and [then a fix](https://github.com/robrant/pi-setup/issues/6).**

### Wifi

It's assumed that the Pi is currently connected via Ethernet to your router.
If you want this playbook to configure wifi, you'll need your Wifi SSID and
your Wifi Pre-shared Key (PSK). Edit the file `playbook.yml` and enter the
details in the relevant vars fields.

	  vars:
    	- wifi_ssid: "<your wifi SSID in here>"
    	- wifi_psk: "<your wifi pre-shared key/password here>"

### Hostname

You can use the `hostname` tag to change the system hostname of your Pi and change references to it in the `hosts` file in this playbook. The `change_hostname` variable determines whether to change the system hostname (in `/etc/hostname`) or not. Change the following variables in `playbook.yml`:

		vars:
			- new_hostname_prefix: <your new hostname prefix>
			- change_hostname: ( true | false ) # (default is yes)

## Keyboard and Timezone Defaults
Sensible (for me) defaults are provided for the keyboard layout and timezone.
To change them, edit `playbook.yml`, changing:

    vars:
        - keyboard : <your keyboard code>
        - timezone : <your timezone>

For a list of timezones, see the `tz` field in [this table](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

## Running

Run the playbook

		$> cd pi-setup
	  $> ./run

You can run parts of the playbook using tags in the playbook. A couple of examples:

	  $> ./run base # recommended before any others.
		$> ./run wifi
		$> ./run hostname
		$> ./run pi-user

## Password prompts

You will be prompted for a password twice - once for the SSH login of the `pi`
user and once for that user to `sudo`. The default password for the `pi` user is `raspbian`.

You will also be prompted for a new password. If you don't want to change the password, just hit 'Enter'.

If I can get the password-less SSH working, that should go away.
