# pi-guzzler
Raspberry Pi image for connecting a personal server to a dedicated IP address through vpn

The problem:
A personal server behind a firewall (for which access is not available) needs to be accessed from the internet.  

The solution:
A dedicated raspberry pi with usb ethernet dongle (which gives it two ports). The raspberry pi connects via vpn to a service with port forwarding and dedicated ip address ([purevpn](https://www.purevpn.com/dedicated-ip) for example). All traffic on all ports from the ip address is forwarded from the vpn through the raspberry pi and to the personal server.

## Installation instructions
[PiGuzzler](https://www.purevpn.com/dedicated-ip)
