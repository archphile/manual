#Introduction

Archphile (http://archphile.org) is an ArchlinuxARM (https://archlinuxarm.org) based Linux distribution with additional packages and configuration in order to transform your board to a k.i.s.s. (keep it simple, stupid!) computer transport.

It currently supports all Armv7/Armv8 Raspberry Pi and Odroid C2 devices.

Archphile uses MPD (Music Player Daemon):

https://www.musicpd.org

MPD is a media player that acts as a network server and needs a client to use it with.

Archphile uses YMPD (https://ympd.org) MPD client by default. However you can use it with any other available MPD client.

Below you will find most of the information needed in order to successfully configure your board with Archphile.  

Please note that you will need to connect via ssh in order to apply all the required changes.



##Finding the IP address for the first time

In order to connect to your Archphile board, you need to find its IP address. This can be done by using an Android/IOS app like Fing (https://www.fing.io), or via the web interface of your router.

If your system supports bonjour/avahi, then you can use http://archphile.local in order to access the web interface or to connect via SSH (with putty – please see below).

After connecting for the first time, it’s strongly suggested to set up a static network IP.



##Connect Via SSH

Connecting via ssh is pretty easy. If you use Windows you will need putty:
 
https://www.putty.org


Linux and Mac OS X users can simply use a terminal application and connect (assuming that Archphile uses the IP 192.168.1.142) using the following command:

 
	ssh root@192.168.1.142

##File editing with nano

Another very useful tool is nano editor. Archphile configuration is based on plain text file editing. For this purpose you will need an editor. For example, let’s say you want to edit /etc/mpd.conf file. All you need to do is:


	nano /etc/mpd.conf


When you finish editing the file, press CTRL + X, then press Y and ENTER and that’s it. Your edited file is now saved and you ‘re ready to go.


Systemd services

Most of the programs found in Archphile are handled via systemd services. Some useful commands are:

	systemctl enable|reenable|disable|start|stop|restart <blabla>

where <blabla> can be mpd, ympd, upmpdcli, shairport-sync, roonbridge, etc..
<div class="pagebreak"></div>

#1.0 System Configuration

##1.1 Root Password

The default password for root is archphile. It is strongly recommended that you change this password the first time you boot Archphile. This can be done using the following command:

	passwd


##1.2 Timezone and NTP server configuration

Timezone/NTP default settings are set up for the country I live in. If you want to change them you will need to do the following:

	rm /etc/localtime

and then link to the appropriate time zone.

	ln -s /usr/share/zoneinfo/Europe/Athens /etc/localtime

The above command is just an example. If you want to explore all available locations, please use the following command:

	ls -la /usr/share/zoneinfo

In order to change NTP servers, you will need to edit the following file:

	rm /etc/ntp.conf

**Note: **Modifying the time zone and NTP servers is completely optional.



#2.0 Network configuration

Archphile default network configuration uses DHCP. It's strongly recommended to change to static IP. In order to do this you must edit the following file:

nano /etc/netctl/archphile-network

The active configuration by default (for DHCP) is the following:

	Description='A basic dhcp ethernet connection'
	Interface=eth0
	Connection=ethernet
	IP=dhcp
	ExecUpPost='/usr/bin/ntpd -gq || true'

Lets assume that the IP of your router is 192.168.1.1 and you want to use the static IP 192.168.1.142 for the Archphile board. The first step is to comment the DHCP section. Now, uncomment the static IP network and change the IP to match your setup:

	#Description='A basic dhcp ethernet connection'
	#Interface=eth0
	#Connection=ethernet
	#IP=dhcp
	#ExecUpPost='/usr/bin/ntpd -gq || true'

	Description='A basic ethernet connection with static ip'
	Interface=eth0
	Connection=ethernet
	IP=static
	Address=('192.168.1.142/24')
	Gateway='192.168.1.1'
	ExecUpPost='/usr/bin/ntpd -gq || true'

The last step is to reenable archphile-network:

	netctl reenable archphile-network

Now reboot with:

	systemctl reboot

Note: The use of a static IP means that the network configuration is dependent on the router. If you plan to use your Archphile board with another router than the one you’ve set it up for, make sure that the new one supports the same IP address range. A good idea is to revert to DHCP configuration, boot with the new router and change to static IP again at a later stage.

