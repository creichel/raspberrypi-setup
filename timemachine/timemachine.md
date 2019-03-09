# Use Raspberry Pi as Time Machine
This short document collects all information to quickly install time machine functionality on the Raspberry Pi via Netatalk 3.1.12 (Dec 2018). I am avoiding using `apt-get install netatalk` because it has some errors in my last projects which has killed my whole Raspberry Pi installation.

## Requirements: hard drive
Please make sure that you have connected a hard drive to the Raspberry Pi and have configured it correctly (like described in `../external-hdd/external.hdd` in this repo).

## 1. Install requirements for netatalk

`sudo aptitude -y install build-essential libevent-dev libssl-dev libgcrypt11-dev libkrb5-dev libpam0g-dev libwrap0-dev libdb-dev libtdb-dev avahi-daemon libavahi-client-dev libacl1-dev libldap2-dev libcrack2-dev systemtap-sdt-dev libdbus-1-dev libdbus-glib-1-dev libglib2.0-dev libio-socket-inet6-perl tracker libtracker-sparql-1.0-dev libtracker-miner-1.0-dev`

Replace `stretch` with `jessie` in `/etc/apt/sources.list` file

update list of approachable libs by running
`sudo apt-get update`

Install missing dependencies via
`sudo aptitude install build-essential libmysqlclient-dev`

Replace back `jessie` with `stretch` in `/etc/apt/sources.list` file

Now we are ready to obtain current version of Netatalk sources from the official page:
`wget http://prdownloads.sourceforge.net/netatalk/netatalk-3.1.12.tar.gz`
`tar -xf netatalk-3.1.12.tar.gz`

Configure compilation
```
./configure \
        --with-init-style=debian-systemd \
        --without-libevent \
        --without-tdb \
        --with-cracklib \
        --enable-krbV-uam \
        --with-pam-confdir=/etc/pam.d \
        --with-dbus-daemon=/usr/bin/dbus-daemon \
        --with-dbus-sysconf-dir=/etc/dbus-1/system.d \
        --with-tracker-pkgconfig-version=1.0
```

Compile
`make`

Install
`sudo make install`

Check if netatalk is running
`netatalk -V`

## 2. Configuration of netatalk

Open the config file of avahi
`sudo nano /etc/avahi/services/afpd.service`

Add AFP and time capsule information to it by pasting the following content:
```
<?xml version="1.0" standalone='no'?><!--*-nxml-*-->
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
    <name replace-wildcards="yes">%h</name>
    <service>
        <type>_afpovertcp._tcp</type>
        <port>548</port>
    </service>
    <service>
        <type>_device-info._tcp</type>
        <port>0</port>
        <txt-record>model=TimeCapsule</txt-record>
    </service>
</service-group>
```

## 4. Configuration of AFP protocol

Edit AFP protocol configuration
`sudo nano /usr/local/etc/afp.conf`

Paste the following code there:
```
[Global]
 mimic model = TimeCapsule8,119
 log level = default:warn
 log file = /var/log/afpd.log
 uam list = uams_guest.so

[Time Machine]
 path = /media/{YOUR FOLDER OF THE HDD}
 time machine = yes
```

You can explore the possibilities of settings here: http://netatalk.sourceforge.net/3.1/htmldocs/afp.conf.5.html
As well as here: https://wiki.archlinux.org/index.php/Netatalk

Mine has a bit more options, including a second hard drive and home directories:

```
[Global]
 mimic model = TimeCapsule8,119
 log level = default:warn
 log file = /var/log/afpd.log
 uam list = uams_guest.so

[Homes]
 basedir regex = /home

[Time Machine]
 path = /media/timemachine
 time machine = yes

[Shared Media]
 path = /media/media
 ```


## 5. Set autostart and start daeomons

Activate the daemons to start up automatically
```
sudo systemctl enable avahi-daemon
sudo systemctl enable netatalk
```

Start daemons
```
sudo service avahi-daemon start
sudo service netatalk start
```
