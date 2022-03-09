# pi-guzzler
Raspberry Pi image for connecting a personal server to a dedicated IP address through vpn

The problem: A personal server behind a firewall (for which access is not available) needs to be accessed from the internet.

The solution: A dedicated raspberry pi with usb ethernet dongle (which gives it two ports). The raspberry pi connects via vpn to a service with port forwarding and dedicated ip address (purevpn for example). All traffic on all ports from the ip address is forwarded from the vpn through the raspberry pi and to the personal server.


Note: to date this solution has been tested with the following VPN providers:
 - PureVPN

It could be modified to work with other providers supporting openvpn service, but that is beyond the scope of this guide.

## Requirements

+ Raspberry Pi model B, B+, 2B, 3B, 3B+, or 4B
+ PureVPN account 
+ 8GB (suggested minimum) SD card
+ Raspberry Pi OS Lite operating system image
+ USB keyboard

1. The Installation Guide can be found in the project wiki.
[installation Guide Part 1:](part1/README.md)
2. [installation Guide Part 2:](part2/README.md)
3. [installation Guide Part 3:](part3/README.md)
