## Installing Network Time Protocol (NTP)

The Network Time Protocol (NTP) is a protocol used to synchronize computer system clock automatically over the networks. The machine can have the system clock use Coordinated Universal Time (UTC) rather than local time. NTP server is important component in every enterprise system since it gets the network time synchronized, scripts execute on time, backup jobs done and more.

Linux distributions such as this one that ships with `systemd`, you can sync time via `timesyncd.conf` file.

```sh
nano /etc/systemd/timesyncd.conf
```

Add or uncomment the following lines after `[Time]` statement, as illustrated in the below excerpt:

```
[Time]
NTP=0.asia.pool.ntp.org 1.asia.pool.ntp.org 2.asia.pool.ntp.org 3.asia.pool.ntp.org
FallbackNTP=ntp.ubuntu.com 0.arch.pool.ntp.org
```

After editing the file, issue the `timedatectl` command to activate the NTP client build in `systemd`.

```sh
timedatectl set-ntp true
timedatectl status
```

Always try to query and sync the time with the closest NTP servers available for your zone. The list of the NTP server pools can be found at [http://www.pool.ntp.org/en/](http://www.pool.ntp.org/en/)
