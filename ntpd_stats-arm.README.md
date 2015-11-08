## NTPD and Performance Stats

This package (ntpd_stats-arm.tar.gz) provides a NTP Daemon (ntp-4.2.8p4) for ASUSWRT/Merlin firmwares. At the moment it's only for ARM based routers.

### Pre-requisite

Entware installed

### Installation

**Step 1:** Install **rrdtool** and **wget** packages from Entware. 

We need rrdtool for graphing. wget is for retrieving the tarball from github.

* `opkg install rrdtool`
* `opkg install wget`

**Step 2:** Copy /www to /opt/var/www in preparing WebGUI to run from /opt/var/www
* `tar cf - /www | tar -C /opt/var -xvf -`

**Step 3:** Retrieve and install ntpd_stats-arm.tar.gz 

**WARNING:** the tarball has the following content. If you notice a conflict with your current setup, you want to make a backup of your existing files first before proceed, and then do a merge before proceed to Step 4.
```
/jffs/bin/ntpd
/jffs/bin/ntpq
/jffs/bin/ntpstats.sh
/jffs/etc/ntp.conf
/jffs/configs/fstab
/opt/etc/init.d/S77ntpd-custom
/opt/var/spool/ntp/Tools_NtpdStats.asp
/opt/var/spool/ntp/stats.rrd
```
Once confirm safe to proceed, run the following

* `wget --no-check-certificate -O - https://github.com/kvic-z/goodies-asuswrt/blob/master/ntpd_stats-arm.tar.gz?raw=true | tar -C / -xzf -`

**Step 4:** Run the following command to manually start ntpd as litmus test

* `cp /jffs/configs/fstab /etc; mount -a`
* `/opt/etc/init.d/S77ntpd-custom start`
* `/jffs/bin/ntpstats.sh`

`mount -a` mounts `/opt/var/www` to `/www`. No worries. It's not a destructive operation. The original content of `/www` still resides safely in ROM.

Stop and check each command if it has error. Or else proceed to Step 5.

**Step 5:** Patch files for WebUI and restart httpd
* `cp /opt/var/spool/ntp/Tools_NtpdStats.asp /www`
* `sed -i 's/Other Settings");/Other Settings", "NTP Daemon");/' /www/state.js`
* `sed -i 's/therSettings.asp");/therSettings.asp", "Tools_NtpdStats.asp");/' /www/state.js`
* `service restart_httpd`

Stop and check each command if it has error. If thing goes well, now you shall be able to see NTP Daemon inside Tools on WebUI.

**Step 6:** Create a cron job to collect stats by adding the following line to `/jffs/scripts/services-start`

* `cru a NtpdStats "*/5 * * * * /jffs/bin/ntpstats.sh"`

**Step 7:** Append the following two lines to the end of `/jffs/scripts/post-mount`

* `mount -a`
* `service restart_httpd`

**Step 8:** Reboot your router and enjoy!

You may want to reboot now to confirm everything stay.

### Tune your NTP Daemon Config

Please check the "server" entries in /jffs/etc/ntp.conf. You want to replace those ip address with a time server near you. Preferrably put some stratum 1 servers there.

### Uninstall
* unstall rddtool and wget through Entware
* remove the lines you added to /jffs/scripts/post-mount and /jffs/scripts/services-start
* `rm /jffs/bin/ntpd`
* `rm /jffs/bin/ntpq`
* `rm /jffs/bin/ntpstats.sh`
* `rm jffs/etc/ntp.conf`
* `rm /jffs/configs/fstab`
* `rm /opt/etc/init.d/S77ntpd-custom`
* `rm /opt/var/spool/ntp/Tools_NtpdStats.asp`
* `rm /opt/var/spool/ntp/stats.rrd`
* `rm -rf /opt/var/www`
* `reboot`

### Forum discussion

http://www.snbforums.com/threads/ntp-daemon-for-asuswrt-merlin.28041/
