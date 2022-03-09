# pi-guzzler
Raspberry Pi image for connecting a personal server to a dedicated IP address through vpn

The problem:
A personal server behind a firewall (for which access is not available) needs to be accessed from the internet.  

The solution:
A dedicated raspberry pi with usb ethernet dongle (which gives it two ports). The raspberry pi connects via vpn to a service with port forwarding and dedicated ip address ([purevpn](https://www.purevpn.com/dedicated-ip) for example). All traffic on all ports from the ip address is forwarded from the vpn through the raspberry pi and to the personal server.

1. The Installation Guide can be found in the project wiki.
[installation Guide Part 1:](pi-guzzler.wiki/2.Installation-Guide-Part-1.md)

2. [installation Guide Part 2:](pi-guzzler.wiki/Installation-Guide-Part-2%3A-Install-and-configure-supporting-services.md)

3. [installation Guide Part 3:](pi-guzzler.wiki/Installtion-Guid-Part-3%3A-Install-web-server.md)
