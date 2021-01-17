# How to set up cod.m CC2538 + CC2592 Raspberry Pi Module for Home Assistant

Biggest hurdle: Accessing Raspberry UART when running Hassio

I wrote this as a comprehensive guide to install the cod.m CC2538 + CC2592 Raspberry Pi Funcmodul v0.3.
It required some info that I was able to gather from the internet in several places. It was written in Januari 2021. Things might have changed since then.
I am using windows to set this all up.

## Getting an SSH Key

1. Install Putty
	- go to https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
	- download the .msi and run it.
2. PuTTYgen should be in yout start menu (Windows key + type 'PuTTYgen' to find it). Run it.
3. Changing setings is optional; I left it to default. Press the 'Generate' button and move your mouse over the blank area to generate some randomness.
4. If you want to add some security to to using the key you can add a password here. I did. 
	- Save it somewhere safe by pressing 'Save private key'. Name it whatever you want.
	- Now you have a file you can always open again by PuTTYgen and the public key will be visible again.
5. The public key we want will not be correctly saved by the 'Save public key' button. You accomplish this by doing the following:
	- Copy the public key from the text field. Be sure to copy all of it. 
	- Paste it in an advanced text editot like Notepad++ or Visial Studio Code. They can be found on the internet and are free.
	- The default encoding is normally UTF-8. Set this to ANSI. (In notepad++ you select Encoding -> Convert To ANSI)
	- The default line endings are Windows (CR LF). Set this to Unix (LF) (In notepad++ you right click in the bottom right where it states: CRLF and select LF.
	- Save it somewhere and name it `authorized_keys`.

## Setting the SSH Key

There are 2 ways to get the key onto the Rapberry Pi.

### Usb Stick

1. Prepare a USB stick:
	- Copy the File we just created to a USB drive that is formatted FAT, ext4 or NTFS. 
	- Give it the name : CONFIG
	- Copy the `authorized_keys` in the root.
	- Plug in into your Raspberry Pi.
2. Now either:
	- Import it: Go to your HA interface and click 'Supervisor' -> 'System'. Under 'Host' select 'Import from USB'. It might be hidden under the tripple dots.
	- Reboot Home Assistant

### Alternate method

- Shut down Home Assistant
- Get the Micro-SD card an put it  in your pc.
- Create a folder named CONFIG
- Copy the file in there
- Put the card back
- Start up your system

## Connecting with PuTTY

1. Start up Putty
2. Enter the up or hostname into the field.
3. Use port 22222
4. Optionally give it a name and hit 'Save'.
5. In the left menu under 'Connection',  select 'SSH' -> 'Auth'. 
6. In the Authentication Parameters click 'Browse...'
7. Select yout private key (ppk file)
8. Click 'Open'
9. Log in as `root`, and use the password you set.

## Editing the config.txt file

After you have logged in...
1. Type `login`
2. Make a backup; Type `cp /mnt/boot/config.txt /mnt.boot.config.bak`
3. vi is the only editor. Type `vi mnt/boot/config.txt`
	- If you have no idea how vi works (I did not); use the link below as a reference
4. Enable the line `enable_uart=1` by removing the `#`
5. Under `[all]` add the line `dtoverlay=disable-bt` to disable it.
	- Even though I have a Pi4, this worked for me.
6. You also need to disable hciuart. However it was not set up. You can check this by running `systemctl status hciuart`
	- If it iss found disable it `systemctl disable hciuart`

## Adding it to Home Assistant

### Hardware

1. Turn off the Raspberry pi
2. Connect the module in the correct way.
3. Power up

### Software

1. Go to 'Configuration' -> 'Integrations' 
2. Click '+ Add Integration' icon in the bottom right and search for ZHA (Select Zigbee Home Automation)
3. For me it configured automatically. But you can look at the link at the resources

### Adding a device

1. Click on the ZHA Integration (1 device)
2. In the bottom right click '+ Add Device'

## Resources:

1. Access with PuTTY:
	- I followed this guide to get access to the OS:
	https://developers.home-assistant.io/docs/operating-system/debugging/
	- It links to a page which describes how to create the SSH key file:
	https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-putty/
2. Getting to the file; after I was logged in I used some info on this thread to get it running
	- https://community.home-assistant.io/t/how-to-access-config-txt-in-hassio/139330/24
3. Info on 'vi'
	- https://www.washington.edu/computing/unix/vi.html
4. Read more about UART configuration:
	- https://www.raspberrypi.org/documentation/configuration/uart.md
5. Adding the device to Home Assistant
	- https://www.home-assistant.io/integrations/zha
