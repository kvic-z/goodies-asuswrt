## NTPD and Performance Stats

This package (ntp_stats-arm.tar.gz) provides a NTP Daemon (ntp-4.2.8p4) for ASUSWRT/Merlin firmwares. 

### Pre-requisite

Entware installed

### Installation

**Step 1:** Install **rrdtool** and **wget** (optional) packages from Entware. 

We need rrdtool for graphing. wget is for retrieving the tarball (in Step 3) from github. You don't need to install wget if you can manually get the tarball to your router through other mean.

* `opkg install rrdtool`
* `opkg install wget`

**Step 2:** Copy /www to /opt/var/www in preparing WebGUI to run from /opt/var/www
* `tar czf /jffs/www.tar.gz /www`
* `cd /opt/var`
* `tar xzf /jffs/www.tar.gz`
* `rm /jffs/www.tar.gz`

**Step 3:** Retrieve and install ntpd_stats-arm.tar.gz 

**WARNING:** the tarball has the following content:
```
/jffs/bin/ntpd
/jffs/bin/ntpq
/jffs/bin/ntpstats.sh
/jffs/etc/ntp.conf
/jffs/configs/fstab
/opt/etc/init.d/S77ntpd-custom
/opt/var/spool/ntp/Tools_NtpdStats.asp
/opt/var/spool/ntp/state.js
/opt/var/spool/ntp/stats.rrd
```
If you notice a conflict with your current setup, you want to make a backup of your existing files first. Then proceed with

* `cd /jffs`
* `wget --no-check-certificate https://github.com/kvic-z/goodies-asuswrt/blob/master/ntpd_stats-arm.tar.gz`
* `cd /`
* `tar xzf /jffs/ntp_stats-arm.tar.gz`
* `rm /jffs/ntp_stats-arm.tar.gz`

**Step 4:** Create a cron job to collect stats by adding the following line to `/jffs/scripts/services-start`

`cru a NtpdStats "*/5 * * * * /jffs/bin/ntpstats.sh`

**Step 5:** Patch files for WebUI
* `cp /opt/var/spool/ntp/Tools_NtpdStats.asp /opt/var/www`
* `cp /opt/var/spool/ntp/state.js /opt/var/www`

**Step 6:** Add the following line to your `/jffs/scripts/post-mount`

* `mount -a`

This will mount /opt/var/www to /www as specified in `/jffs/configs/fstab`. No worries. It's not a destructive operation. The original content of /www still resides safely in ROM.

**Step 7:** Restart WebGUI
* `service restart_httpd`

Now you shall be able to see NTP Daemon inside Tools

**Step 8:** Reboot your router and enjoy!
Once Step 6 confirms working. You may want to reboot to confirm everything stay.

### Tune your NTP Daemon Config

Please check the "server" entries in /jffs/etc/ntp.conf. You want to replace those ip address with a time server near you. Preferrably put some stratum 1 servers there.

### Uninstall
* remove the lines you added to /jffs/scripts/post-mount and /jffs/scripts/services-start
* `rm /jffs/bin/ntpd`
* `rm /jffs/bin/ntpq`
* `rm /jffs/bin/ntpstats.sh`
* `rm jffs/etc/ntp.conf`
* `rm /jffs/configs/fstab`
* `rm /opt/etc/init.d/S77ntpd-custom`
* `rm /opt/var/spool/ntp/Tools_NtpdStats.asp`
* `rm /opt/var/spool/ntp/state.js`
* `rm /opt/var/spool/ntp/stats.rrd`
* `rm -rf /opt/var/www`
* `reboot`

### Forum discussion

http://www.snbforums.com/threads/ntp-daemon-for-asuswrt-merlin.28041/
