# Add mediaserver (PLEX) to raspberry pi
PLEX is a really nice piece of software to access your media (movies, films, etc.) comfortably. To run PLEX and to access your library, you have to setup a PLEX media server.

## 1. become root

We need sudo throughout all steps. To make it more easier for us, we just enter sudo mode.
`sudo su`


## 2. Add public key

`wget -O - https://dev2day.de/pms/dev2day-pms.gpg.key | apt-key add -`

## 3. Add PLEX media server repo

`echo "deb https://dev2day.de/pms/ stretch main" >> /etc/apt/sources.list.d/pms.list`

## 4. Activate https

`apt-get -y install apt-transport-https`

## 5. Update the repos

`apt-get update`

## 6. Install PLEX media server

`apt-get -y --allow-unauthenticated install plexmediaserver-installer`

## 7. Add apache2 for proxy / port forwarding
You can normally access your media by calling the IP address with the port `32400`. To enter ports is never cool, so we want to get rid of them by using apache and port forwarding.

Install apache
`apt-get install apache2`

Set your localhost as server name. All other configs need this to access the local ports.
`echo "ServerName localhost" >> /etc/apache2/conf-available/servername.conf`

Enable the necessary modules for port forwarding
`a2enmod proxy proxy_http proxy_http2 headers rewrite`

Configure port forwarding and remove the necessary `/web` directory. Make a new plex conf file
`nano /etc/apache2/conf-available/plex.conf`

Paste the following code into the new file:
```
<VirtualHost _default_:80>

  <Proxy *>
    Order deny,allow
    Allow from all
  </Proxy>

  ProxyRequests Off
  ProxyPreserveHost On
  ProxyPass /web/ http://127.0.0.1:32400/web/
  ProxyPassReverse /web/ http://127.0.0.1:32400/web/
</VirtualHost>
```

Activate our configurations
`a2enconfig servername plex`

Disable the standard apache page (if you don't need it)
`a2dissite 000-default.conf`

Restart apache
`systemctl restart apache2`

## 8. Get rid of your IP address and use a `.local` domain

How about accessing the media library just by entering `home.local` in your browser? It sounds easy like calling Netflix - so we also need this simplicity. Also, it gives us the freedom of dynamic IP addresses.

Setup raspi config
`raspi-config`

Move to `Networks > Hostname` and enter whatever you like.

### Install Bonjour

`apt-get install avahi-daemon`

You can also now connect through SSH via your hostname!

# Links

- Install Plex on Raspberry Pi: https://dev2day.de/plex-media-server-arm/
- Change Port Forwarding: http://matt.coneybeare.me/how-to-map-plex-media-server-to-your-home-domain/
- VirtualHost configuration examples: https://httpd.apache.org/docs/2.4/vhosts/examples.html
- .local domain: https://www.howtogeek.com/167190/how-and-why-to-assign-the-.local-domain-to-your-raspberry-pi/
