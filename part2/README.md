Now that the base OS is installed, we will install and configure the supporting services needed for this project. These services include a DNS forwarder, openvpn, and logrotate.

<h2>Download the project files</h2>

Install `git`:

`sudo apt install -y git`

Clone the project repository; this will download the project files to your local system:

`git clone https://github.com/mr-canoehead/vpn_client_gateway.git`

<h2>Install the DNS forwarder</h2>

<br>
The DNS forwarder will accept DNS requests from clients and forward them to real name servers (e.g. Google's 8.8.8.8). This will prevent DNS leaks when clients are using the Pi as a router/gateway. We'll use <b>dnsmasq</b> as our DNS forwarder for this project:

`sudo apt install -y dnsmasq`

Create a resolver config file for dnsmasq.

`sudo nano /etc/dnsmasq-resolv.conf`

The contents should be:
```
nameserver 1.1.1.1
nameserver 1.0.0.1
```

Edit /etc/dnsmasq.conf:

`sudo nano /etc/dnsmasq.conf`

Uncomment the `domain-needed` and `bogus-priv` settings:

```
# Never forward plain names (without a dot or domain part)
domain-needed
# Never forward addresses in the non-routed address spaces.
bogus-priv
```

Uncomment the ‘interface’ setting, set it to the name of your gateway interface, e.g. "eth0":
```
# If you want dnsmasq to listen for DHCP and DNS requests only on
# specified interfaces (and the loopback) give the name of the
# interface (eg eth0) here.
# Repeat the line for more than one interface.
interface=eth0
```

Uncomment the `resolv-file` setting, change it to:

`resolv-file=/etc/dnsmasq-resolv.conf`

Save the file.

Restart the dnsmasq service to apply these changes:

`sudo service dnsmasq restart`

<h2> Install dnsutils</h2>

<details><summary><i>Click for Pi-hole instructions (ignore for normal installations)</i></summary>
<p>
<hr>
The Pi-hole installation script has already installed the <b>dnsutils</b> package, so skip ahead to:

[Install openvpn](https://github.com/mr-canoehead/vpn_client_gateway/wiki/Installation-Guide---Part-2:-Install-and-configure-supporting-services#install-openvpn)
<hr>
</p>
</details>
<br>
The status monitor script uses the `dig` command to test DNS queries, `dig` is included in the `dnsutils` package:

`sudo apt install -y dnsutils`

<h2>Install openvpn</h2>
For this project, we'll use <b>openvpn</b> to provide virtual private network connections. This guide assumes the use of the Private Internet Access VPN service. If you are using the ExpressVPN, PureVPN, Newshosting VPN, or NordVPN service, please refer to the notes in the following page:

https://github.com/mr-canoehead/vpn_client_gateway/wiki/VPN-Providers

Install openvpn:

`sudo apt install -y openvpn`

Download the openvpn configuration files from the Private Internet Access site:

`wget https://www.privateinternetaccess.com/openvpn/openvpn.zip`

Extract the files in the pi user's home folder:

`unzip openvpn.zip -d /home/pi/openvpn`

Copy the files Certificate Revocation List and Certificate Authority certificate files to /etc/openvpn/client:

`sudo cp openvpn/crl.rsa.2048.pem openvpn/ca.rsa.2048.crt /etc/openvpn/client`

Create the file /etc/openvpn/client/auth.txt. This file will contain your Private Internet Access login
credentials:

`sudo nano /etc/openvpn/client/auth.txt`

Sample contents:
```
p8213552
svpa214S
```
The first line is your user name, the second line is your password. Make sure that there are no trailing spaces on either line as this will cause authentication to fail.

Make the file read/writeable only by root:

`sudo chmod 600 /etc/openvpn/client/auth.txt`

Create the file /etc/openvpn/client/vpncgw.conf:

`sudo nano /etc/openvpn/client/vpncgw.conf`

The file contents should be as follows:
```
client
dev tun0
proto udp
remote us-siliconvalley.privacy.network 1198
resolv-retry infinite
keepalive 10 60
nobind
persist-key
redirect-gateway
cipher aes-128-cbc
auth sha1
tls-client
remote-cert-tls server
auth-user-pass auth.txt
auth-retry nointeract
pull-filter ignore "auth-token"
comp-lzo
verb 1
reneg-sec 0
crl-verify crl.rsa.2048.pem
ca ca.rsa.2048.crt
management 0.0.0.0 7505
script-security 2
up "/etc/openvpn/client/change_dns.sh up"
down "/etc/openvpn/client/change_dns.sh down"
```

Change ownership of the openvpn configuration file:

`sudo chown www-data:www-data /etc/openvpn/client/vpncgw.conf`

Copy the DNS update script:

`sudo cp vpn_client_gateway/application/scripts/change_dns.sh /etc/openvpn/client/change_dns.sh`

<img src="https://gist.github.com/mr-canoehead/09fe6f467f0d080862afd8802045e302/raw/43be712d4fef3918db22225f64500251ba387ab3/warn.png" alt="warning" width="30"> <i>Note: if you are not using Private Internet Access as your VPN service provider you'll need to modify the DNS update script which uses PIA DNS servers by default; modify the IP addresses to match your VPN service provider's nameserver addresses. </i>

Enable the openvpn service so that it will start using our configuration file:

`sudo systemctl enable openvpn-client@vpncgw`

Start the openvpn service:

`sudo service openvpn-client@vpncgw start`

If you want to follow the connection process in the logs, open another ssh session and execute the
following command:

`sudo tail -f /var/log/syslog`

Any openvpn connection errors will be shown.

If you have issues connecting, try running openvpn manually by doing the following:

change directory to /etc/openvpn/client:

`cd /etc/openvpn/client`

stop the openvpn service

`sudo service openvpn-client@vpncgw stop`

run openvpn manually:

`sudo openvpn --config vpncgw.conf`

openvpn will output a log of its connection sequence, any errors should be displayed.

<h2>Configure logrotate</h2>

To prevent log files from eating up disk space, we'll use <b>logrotate</b>. This service comes pre-installed in the Raspbian build. Edit the logrotate configuration file:

`sudo nano /etc/logrotate.conf`

I would suggest changing the rotate frequency to daily, and the backlogs to 4. Here is what the top
section of the file should look like:
```
# see "man logrotate" for details
# rotate log files daily
daily
# keep 4 days worth of backlogs
rotate 4
# delete any logs older than 4 days
maxage 4
```
<h2>Configure iptables</h2>

The firewall used by Raspbian is configured using <b>iptables</b>. It allows or blocks connections based on a set of rules. In order to allow the forwarding, http, and dns connections needed for this project we'll start with a working set of iptables rules. A script to load these rules is included with the project files.

The firewall configuration script uses the <b>iptables-persistent</b> utility to save the firewall rules so that they persist across reboots.

Install iptables-persistent:

`sudo apt install -y iptables-persistent`

During the installation, the program will prompt you to save the current iptables rules (ipV4 and ipV6).
Acknowledge both prompts. The iptables rules are now saved and will be re-loaded at boot time.

Run the firewall configuration script to load the iptables rules. You'll find the script in the folder vpn_client_gateway/fw:

`vpn_client_gateway/fw/fw-config`

<img src="https://gist.github.com/mr-canoehead/09fe6f467f0d080862afd8802045e302/raw/43be712d4fef3918db22225f64500251ba387ab3/warn.png" alt="warning" width="30"> <i>Note: the firewall rules include a 'killswitch' that blocks network traffic if the VPN connection drops. If your VPN connection is not working when you load the firewall rules, your Pi won't be able to connect to the internet. Please refer to the [Kill Switch feature](https://github.com/mr-canoehead/vpn_client_gateway/wiki/Kill-Switch-feature) page for additional information.</i>

For additional information on iptables, refer to [Additional Information: Modifying iptables rules](https://github.com/mr-canoehead/vpn_client_gateway/wiki/Modifying-iptables-rules).

<h2>Enable IP forwarding</h2>
Edit sysctl.conf:

`sudo nano /etc/sysctl.conf`

Uncomment the following setting:
```
net.ipv4.ip_forward = 1
```

To enable the change, run the following command:

`sudo sysctl -p /etc/sysctl.conf`

## Configure sudo permissions
The application needs permission to perform various tasks such as managing the openvpn service. These permissions are granted by creating a sudoers file:

`sudo nano /etc/sudoers.d/vpncgw`

The contents should be:

```
www-data ALL=(root) NOPASSWD: /usr/sbin/service
www-data ALL=(root) NOPASSWD: /bin/systemctl
www-data ALL=(root) NOPASSWD: /usr/bin/tail
www-data ALL=(root) NOPASSWD: /usr/sbin/update-rc.d
www-data ALL=(root) NOPASSWD: /sbin/shutdown
www-data ALL=(root) NOPASSWD: /sbin/iptables
www-data ALL=(root) NOPASSWD: /sbin/iptables-save
www-data ALL=(root) NOPASSWD: /bin/su
```

[Proceed to Part 3](../part3/README.md)