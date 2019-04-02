Using NTP Client Server to sync time of all devices in the local network<br/>
@ Setting up NTP Server
1. uninstall ntpdate in the server if there is ntpdate installed
2. install NTP
sudo apt-get install ntp
2. edit ntp config file
sudo nano /etc/ntp.conf
3. add the following code to the ntp config file. Change or add the servers that you want to connect and don't forget to select the region of server that is near with you so the time is correct.
```# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntp/ntp.drift

# Leap seconds definition provided by tzdata
leapfile /usr/share/zoneinfo/leap-seconds.list

# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

# Specify one or more NTP servers.

# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
#pool 0.ubuntu.pool.ntp.org iburst
#pool 1.ubuntu.pool.ntp.org iburst
#pool 2.ubuntu.pool.ntp.org iburst
#pool 3.ubuntu.pool.ntp.org iburst

#server 0.ubuntu.pool.ntp.org iburst
#server 1.ubuntu.pool.ntp.org iburst
#server 2.ubuntu.pool.ntp.org iburst
#server 3.ubuntu.pool.ntp.org iburst

server 2.id.pool.ntp.org iburst
server 0.asia.pool.ntp.org iburst
server 1.asia.pool.ntp.org iburst

server 127.127.1.0
fudge 127.127.1.0 stratum 10


# Use Ubuntu's ntp server as a fallback.
#pool ntp.ubuntu.com

# Access control configuration; see /usr/share/doc/ntp-doc/html/accopt.html for
# details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# might also be helpful.
#
# Note that "restrict" applies to both servers and clients, so a configuration
# that might be intended to block requests from certain clients could also end
# up blocking replies from your own upstream servers.

# By default, exchange time with everybody, but don't allow configuration.
restrict -4 default kod notrap nomodify nopeer noquery limited
restrict -6 default kod notrap nomodify nopeer noquery limited

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# Needed for adding pool entries
restrict source notrap nomodify noquery

# Clients from this (example!) subnet have unlimited access, but only if
# cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust


# If you want to provide time to your local subnet, change the next line.
# (Again, the address is an example only.)
#broadcast 192.168.123.255

# If you want to listen to time broadcasts on your local subnet, de-comment the
# next lines.  Please do this only if you trust everybody on the network!
#disable auth
#broadcastclient

#Changes recquired to use pps synchonisation as explained in documentation:
#http://www.ntp.org/ntpfaq/NTP-s-config-adv.htm#AEN3918

#server 127.127.8.1 mode 135 prefer    # Meinberg GPS167 with PPS
#fudge 127.127.8.1 time1 0.0042        # relative to PPS for my hardware

#server 127.127.22.1                   # ATOM(PPS)
#fudge 127.127.22.1 flag3 1            # enable PPS API

restrict  10.129.15.0 mask 255.255.0.0 nomodify notrap
restrict 127.0.0.1
#broadcast 10.129.15.255
```

4. restart NTP service 
sudo /etc/init.d/ntp restart

5. sync date/time in server using ntp
sudo ntpq -np


@ Setting up NTP Client
1. Uninstall ntpdate in client
sudo apt-get remove ntpdate
2. Install NTP
sudo apt-get install ntp
3. edit NTP config file
sudo nano /etc/ntp.conf
4. add the following code and select the local ntp server in your local network
```
# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntp/ntp.drift

# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable


# You do need to talk to an NTP server or two (or three).
#server ntp.your-provider.example

# pool.ntp.org maps to about 1000 low-stratum NTP servers.  Your server will
# pick a different set every time it starts up.  Please consider joining the
# pool: <http://www.pool.ntp.org/join.html>
#pool 0.debian.pool.ntp.org iburst
#pool 1.debian.pool.ntp.org iburst
#pool 2.debian.pool.ntp.org iburst
#pool 3.debian.pool.ntp.org iburst

#server 2.id.pool.ntp.org iburst
#server 0.asia.pool.ntp.org iburst
#server 1.asia.pool.ntp.org iburst
server 10.129.15.24 iburst

# Access control configuration; see /usr/share/doc/ntp-doc/html/accopt.html for
# details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# might also be helpful.
#
# Note that "restrict" applies to both servers and clients, so a configuration
# that might be intended to block requests from certain clients could also end
# up blocking replies from your own upstream servers.

# By default, exchange time with everybody, but don't allow configuration.
restrict -4 default kod notrap nomodify nopeer noquery limited
restrict -6 default kod notrap nomodify nopeer noquery limited

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# Needed for adding pool entries
restrict source notrap nomodify noquery

# Clients from this (example!) subnet have unlimited access, but only if
# cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust


# If you want to provide time to your local subnet, change the next line.
# (Again, the address is an example only.)
#broadcast 192.168.123.255

# If you want to listen to time broadcasts on your local subnet, de-comment the
# next lines.  Please do this only if you trust everybody on the network!
#disable auth
#broadcastclient

restrict 10.129.15.24
restrict 127.0.0.1
```

5. restart NTP service
sudo /etc/init.d/ntp restart

@ to update date/time in client using ntp or sync date/time from local PC server
1. sudo service ntp stop
2. sudo ntpd -gq
3. sudo service ntp start

@ Schedule sync date/time in client to server everyday at midnight
1. make bash file .sh and add the script below to sync date/time and save to /home/pi/date_time.sh
#!/bin/sh
sudo service ntp stop
sudo ntpd -gq
sudo service ntp start
2. open crontab and add the following schedule to schedule bash script to run every booting and everyday at midnight
```
sudo crontab -e
- @reboot sudo bash /home/pi/date_time.sh
- 0 0 * * * sudo bash /home/pi/date_time.sh
```
3. Check crontab list
sudo crontab -l
4. Date/time on client should be always synchronous with date/time in PC Server. Enjoy it.