## Configuring Firewall

In computing, a firewall is a network security system that monitors and controls incoming and outgoing network traffic based on predetermined security rules. A firewall typically establishes a barrier between a trusted internal network and untrusted external network, such as the Internet.

Uncomplicated Firewall or UFW is an interface to `iptables` that is designed to simplify the process of configuring a firewall.

- Check `/etc/default/ufw` for configurations.
- Check `/etc/ufw` for added rules and backups.

Following commands are useful for managing a startup project

```sh
ufw status verbose
ufw enable
ufw reload
ufw disable
ufw allow ssh
ufw allow smtp
ufw allow http
ufw allow https
ufw delete <RULE NUMBER>
ufw reset
```

For only allowing a specified IP address on development stage:

```sh
ufw allow from <IPAddress> proto tcp to any port <PortNumber>
```

_To allow only IPV4 edit `# nano /etc/default/ufw` and set `IPV6=yes` to `IPV6=no`_

