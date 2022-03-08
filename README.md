# pi-guzzler
Raspberry Pi image for connecting a personal server to a dedicated IP address through vpn

The problem:
A personal server behind a firewall (for which access is not available) needs to be accessed from the internet.  

The solution:
A dedicated raspberry pi with usb ethernet dongle (which gives it two ports). The raspberry pi connects via vpn to a service with port forwarding and dedicated ip address ([purevpn](https://www.purevpn.com/dedicated-ip) for example). All traffic on all ports from the ip address is forwarded from the vpn through the raspberry pi and to the personal server.

## 1. Install the DNS forwarder

A DNS forwarder accepts DNS requests from clients and forwards them to real name servers, for example; 8.8.8.8 for Google. This, in turn, prevents DNS leaks when clients are utilizing the Pi as a router or gateway. We will use dnsmasq as our DNS forwarder for this project:

```
    sudo apt-get install -y dnsmasq
```

## 2. Edit /etc/dnsmasq.conf file

```
    sudo nano /etc/dnsmasq.conf
```
1. Uncomment ‘domain-needed’
2. Uncomment ‘bogus-priv’
3. Uncomment Uncomment ‘interface’ settings, and set it to eth0.
4. Save the file. To continue with the changes, restart the dnsmasq service. 

```
    sudo service dnsmasq restart
```

## 3. Install OpenVPN:

```
    sudo apt-get install -y openvpn
```

## 4. Create openVPN and Download Configuration files

1. create openvpn folder
```
    mkdir ~/openvpn && cd ~/openvpn
```

2. download configuration files
```
    wget https://d32d3g1fvkpl8y.cloudfront.net/heartbleed/linux/linux-files.zip
```

3.  download unzip
```
    sudo apt install unzip
```

4.  
```
    sudo apt install unzip
```