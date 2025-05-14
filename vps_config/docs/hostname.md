## Editing Hosts and Hostname File

The hostname is a translation of your IP address, it is your device ID in the network, like a domain name (a hostname can be a domain name). Hostnameâ€™s function is to make the address easy for humans so we donâ€™t need to remember IP addresses. Two devices canâ€™t have share a hostname, it is unique.

The device's hostname in Linux is stored in two configuration files located in the directory `/etc/`.
The files `/etc/hostname` and `/etc/hosts`.

To verify your current hostname run:

```sh
hostname
```

`systemd` based Linux distro such as this one simply use the `hostnamectl` command to change hostname. To set and see current setting just type the following command:

```sh
hostnamectl set-hostname <newHostname>
hostnamectl
```

The file `/etc/hosts` maps the host and ip addresses, it translates from IP addresses (X.X.X.X) to FQDN e.g. domain.com or @Anything. In this file you can add all the local network devices ip addresses and `hostnames` to avoid domain name resolution. Usually the file hosts has 3 columns, the IP address, the hostname and an alias for the hostname, it can have more than one alias or may not have at all as in the example below.

```
127.0.0.1               localhost
<InstanceIPAddress>		example.com                   <NewHostname>

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

_On the first line `localhost` is preferred by services that are relies on loopback address, you can add any name and translate to desired ip address, keep in mind that `<DropletIPAddress>` only be translated from alias to FQDN to ip address if you have set the FQDN as the RDNS on your instance_

The `/etc/hosts` file act as a local DNS. We can check if the `FQDN` and `<NewHostname>` pointing to the right `<InstanceIPAddress>` by running:

```sh
ping example.com
ping <newHostname>
```

Reboot the system to changes take effect:

`shutdown -r now`

