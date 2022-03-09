## Install the web server
The VPN Client Gateway now uses `nginx` due to its built-in support for WebSockets:

`sudo apt install -y nginx`

<details><summary><i>Click for Pi-hole instructions (ignore for normal installations)</i></summary>
<p>
<hr>

Install the project's web page files:

`sudo cp -R vpn_client_gateway/www/* /var/www/html`

Install the combined site configuration file:

`sudo cp vpn_client_gateway/config/pihole-vpncgw.conf  /etc/nginx/sites-available`

Enable the site by creating a link to the configuration file:

`sudo ln -s /etc/nginx/sites-available/pihole-vpncgw.conf /etc/nginx/sites-enabled/pihole-vpncgw.conf`

Remove the existing link to the default site (it conflicts with the VPN Client Gateway site):

`sudo rm /etc/nginx/sites-enabled/default`

Restart the nginx service:

`sudo service nginx restart`

Now test the web server by accessing the following URL in a browser:

`http://10.1.2.50/index.nginx-debian.html`

Make sure to use the correct IP address for your Raspberry Pi. Your browser should display the <b>'Welcome to nginx!'</b> message.

The combined site configuration file includes a proxy for the Pi-hole web page, e.g.:

`http://10.1.2.50/pihole`

Now skip ahead to:

[Install the application server](https://github.com/mr-canoehead/vpn_client_gateway/wiki/Installation-Guide-Part-3:-Install-web-server,-application-server,-and-web-page-files#install-the-application-server)


<hr>
</p>
</details>
<br>

Install the project's web page files:

`sudo cp -R vpn_client_gateway/www/* /var/www/html`

Install the nginx site configuration file:

`sudo cp vpn_client_gateway/config/vpncgw.conf  /etc/nginx/sites-available`

To enable the website, create a link to the site configuration file:

`sudo ln -s /etc/nginx/sites-available/vpncgw.conf /etc/nginx/sites-enabled/vpncgw.conf`

Remove the existing link to the default site (it conflicts with the VPN Client Gateway site):

`sudo rm /etc/nginx/sites-enabled/default`

Restart the nginx service:

`sudo service nginx restart`

Now test the web server by accessing the following URL in a browser:

`http://10.1.2.50/index.nginx-debian.html`

Make sure to use the correct IP address for your Raspberry Pi. Your browser should display the <b>'Welcome to nginx!'</b> message.

## Install the application server
The VPN Client Gateway application is a Python Flask app that is served by Gunicorn 'Green Unicorn' (a WSGI HTTP server).

Install the Python package installation tool:

`sudo apt install -y python3-pip`

Install the system monitoring package:

`sudo pip3 install psutil`

Install the pathlib package:

`sudo pip3 install pathlib`

Install the speedtest package:

`sudo pip3 install speedtest-cli`

Install the `lxml` XML toolkit:

`sudo apt install -y python3-lxml`

Install the Gunicorn 'Green Unicorn' WSGI HTTP server:

`sudo apt install -y gunicorn3`

Install async workers for Gunicorn:

`sudo pip3 install eventlet`

Install Flask, a Python microframework:

`sudo pip3 install Flask`

Install SocketIO:

`sudo pip3 install flask-socketio`

Create the application directory:

`sudo mkdir /opt/vpncgw`

Change ownership of the directory to www-data:

`sudo chown -R www-data:www-data /opt/vpncgw`

Install the VPN Client Gateway application files:

`sudo cp -R vpn_client_gateway/application/* /opt/vpncgw`

Install the systemd unit file for the VPN Client Gateway application:

`sudo cp vpn_client_gateway/config/vpncgw.service /etc/systemd/system`

Reload the systemd manager config to pick up the change:

`sudo systemctl daemon-reload`

Enable the new service:

`sudo systemctl enable vpncgw`

Start the VPN Client Gateway service:

`sudo service vpncgw start`

## The installation is complete!

The VPN Client Gateway should now be up and running. To access it, point your web browser to the server's url, e.g.:

`http://10.1.2.50`

<i>*Note: if the management web page displays the message "requesting data...", take a look at /var/log/syslog. The most common cause of this issue is a missing Python module which will cause the application to fail on startup. After installing the missing Python module(s), try restarting the vpncgw service.
</i>

<details><summary><i>Click for Pi-hole instructions (ignore for normal installations)</i></summary>
<p>
<hr>

The Pi-hole admin page can be accessed via the server's url, e.g.:

`http://10.1.2.50/pihole`

<hr>
</details>
<br>

<hr>