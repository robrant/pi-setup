# Raspberry PI Base Setup
This repository provides a set of Ansible scripts that you can use to setup your Raspberry PI.

## What it does
 - Upgrades the system.
 - Installs a set of commonly used tools like vim, Git, tmux.
 - Configures NTP to your time zone.
 - Switches the keyboard layout to your preference.
 - Copies your SSH public key to the RPI.
 - Optionally disables password based SSH login.
 - Configures wifi connection - DHCP assigned IP.
 - Changes the RPI system `hostname` (writes that to the hosts file in this repo for future plays).
 - Changes the password of the default user `pi`. It prompts you for the new one.

# Docs

 - [Pi Setup: From nothing to Pi](docs/setup.md)
 - [Run this playbook](docs/running.md)
