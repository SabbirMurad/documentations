## Configuring SSH Daemon

Let's make a backup of the `sshd_config` file first

```sh
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup && \
> /etc/ssh/sshd_config
```

Now edit the SSH daemon `nano /etc/ssh/sshd_config` configuration file and paste following lines at the end of the file.

```
# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd_config was a successor of systems sshd_config.default

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options change a
# default value.

# Login Banner
Banner /etc/issue.net
PrintMotd yes

# Allow Access
AllowUsers <user-account>

# Listening Ports, IPs and protocols
Port 22

# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress ::
#ListenAddress 0.0.0.0
AddressFamily inet
Protocol 2

# HostKeys for protocol version 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

# Logging
PrintLastLog yes
SyslogFacility AUTH
LogLevel INFO

# Authentication:
LoginGraceTime 120
MaxAuthTries 5
MaxSessions 5
PermitRootLogin no
StrictModes yes

# TCP connection
TCPKeepAlive yes
ClientAliveInterval 15
ClientAliveCountMax 55

# Don't read the user's ~/.rhosts and ~/.shosts files
IgnoreRhosts yes

# similar for protocol version 2
HostbasedAuthentication no

# Uncomment if you don't trust ~/.ssh/known_hosts for RhostsRSAAuthentication
IgnoreUserKnownHosts yes

# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication yes
UsePAM yes

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no

# override default of no subsystems
#Subsystem	sftp	/usr/lib/misc/sftp-server
Subsystem	sftp	internal-sftp
```

Now test the edited file by running:

```sh
sshd -t
```

If all goes well, restart the `sshd` to changes take effect:

```sh
systemctl restart sshd
```

_There's no need for any additional configuration for `PAM` authentication to work with sshd!_

