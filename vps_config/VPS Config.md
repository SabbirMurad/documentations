# Ubuntu 20.04 LTS (Focal Fossa)

**The long-term support version of Ubuntu Server, including the Ussuri release of OpenStack and support guaranteed until April 2025.**

## Initial System Wide OS Updates

```sh
apt update && apt list --upgradable && apt upgrade -y && apt autoremove -y
```

_Some VPS's locally modified GRUB is not configured for encrypted volume or swap partition, so it's okay to leave the warning as it is or you could simply remove the crypto package by running:_

```sh
apt remove cryptsetup
```

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

## Installing Build Essentials / Development Tools

The build-essentials package is a reference for all the packages needed to compile a Debian package. It generally includes the **GCC/g++** compilers and libraries and some other utilities. So if you need to install C/C++ compiler, you just need to install build-essential package on your machine. And the build-essential is a meta package that installs many other packages, like G++, GCC, dpkg-dev, make, etc.

Execute the following command to install build-essential package:

```sh
apt install build-essential -y
```

After the installation process is completed, you can confirm your installation by checking for GCC version with the following command:

```sh
gcc --version
```

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

## Managing New User

Since Linux is a multi-user operating system, several people may be logged in and actively working on a given machine at the same time. Security-wise, it is never a good idea to allow users to share the credentials of the same account. In fact, best practices dictate the use of as many user accounts as people needing access to the machine. At the same time, it is to be expected that two or more users may need to share access to certain system resources, such as directories and files. User and group management in Linux allows us to accomplish both objectives.

### Adding a New Regular Account

```sh
adduser <account-name>
```

You may be prompted to set the new user's initial password, and other optional information (such as full name, work phone, etc). This will be stored in `/etc/passwd` using colons as field separators. If not, you can assign a password for the newly created account named `<account-name>` with:

```sh
passwd <account-name>
```

_When a new user is added, a group with the same name is created automatically. This is called a primary group_

### Granting Administrative Privileges

Now we have a new user account with regular account privileges. However, we may sometimes need to do administrative tasks. To avoid having to log out of our normal user and log back in as the `root` account, we can set up what is known as superuser or `root` privileges for our normal account. This will allow our normal user to run commands with administrative privileges by putting the word `sudo` before the command.

To add these privileges to our new user, we need to add the user to the sudo group. By default, on `Ubuntu 20.04`, users who are members of the `sudo` group are allowed to use the `sudo` command.

As `root`, run this command to add your new user to the sudo group:

```sh
usermod -aG sudo <user_name>
```

Now, when logged in as your regular user, you can type sudo before commands to run them with superuser privileges.

## Customizing Bash Prompt

In BASH, we can customize and change the BASH prompt as the way you want by changing the value of **PS1** environment variable.

Customize the **root account** prompt by editing `nano ~/.bashrc` as root and paste the following lines at the end of the file:

```
#Custom color for PS1.

CYAN="\e[0;36m"
BLUE="\e[0;34m"
WHITE="\e[00m"
CEND="\e[m"

RED_BACKGROUND="\e[41m"
GREEN_BACKGROUND="\e[42m"

PS1="\[$WHITE\]\[$RED_BACKGROUND\]\u\[$WHITE\]@\[$CYAN\]\h: \[$BLUE\]\w\[$CEND\]>> "
```

Customize the **user account** prompt by editing `nano ~/.bashrc` as user and paste the following lines at the end of the file:

```
#Custom color for PS1.

CYAN="\e[0;36m"
BLUE="\e[0;34m"
WHITE="\e[00m"
CEND="\e[m"

LIGHT_RED="\e[91m"
LIGHT_GREEN="\e[92m"

PS1="\[$WHITE\]\[$LIGHT_GREEN\]\u\[$WHITE\]@\[$CYAN\]\h: \[$BLUE\]\w\[$CEND\]>> "
```

## Editing SSH Login Banner

A legal banner contains some security warning information or general information, that alerts the user. It can be used for security, legal info, company policy, etc. To display Welcome or Warning message for SSH users before login. Use `issue.net` file to display a banner massages.

`/etc/issue.net` and `/etc/issue` are used to display a banner. `/etc/issue.net` is shown to the users who connect from the network. `/etc/issue` is shown to both local users and network users unless `/etc/issue.net` is present and configured.

Open the following file with the editor:

```sh
nano /etc/issue.net
```

Copy and paste the following lines

```
careful_what_you_wish_for__you_may_receive_it!

```

To configure them to be displayed when you login via SSH, you need to uncomment `#Banner` and specify the desired filename at `/etc/ssh/sshd_config`, like:

```sh
Banner /etc/issue.net
```

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

## Configuring Git

By default git is already installed. Execute the following lines for configuration.

```
git config --global user.name "Full Name"
git config --global user.email "Email Address"
git config --global core.editor nano
git config --global color.ui true
```

To show all configuration run:

```sh
git config --list
```

To count total number of commits:

```sh
git rev-list --all --count
```

Configuration for remote repository

```sh
git remote add origin "url.git"
git remote -v
git pull origin master
git push origin master
```

In case of error: "fatal: refusing to merge unrelated histories" use `--allow-unrelated-histories` flag. For more details run: `man git`.

## Managing Certbot

Certbot is a free, open source software tool for automatically using Let's Encrypt certificates on manually-administrated websites to enable HTTPS.

Install Certbot by running:

```sh
apt install certbot
```

For additional info run: `man certbot`

### Accruing New Certificates

_Make sure to stop any http service on port 80 before running the following command_

```sh
certbot certonly --standalone -d domain.com -d www.domain.com
```

You will find the certificates at `/etc/letsencrypt/live` directory. For renewing the certificates run `# certbot renew`

**IMPORTANT: After generating a new certificate or renewing, make sure you copy `chain.pem` file content and paste at the bottom of the `cert.pem` file. Some TLS library checks cert chain.**

```sh
cat /etc/letsencrypt/live/<domain>/chain.pem
nano /etc/letsencrypt/live/<domain>/cert.pem
```

**NOTE - Prior to template_actix4: Rustls library expect `privkey.pem` file to be start with `-----BEGIN RSA PRIVATE KEY-----` and end with `-----END RSA PRIVATE KEY-----`. Make sure to check on the file content in case of error.**

## Rust (Programming language)

[Installing Rust in Linux](https://www.rust-lang.org/tools/install)

## Systemd Daemon Configuration

`systemd` is a Linux initialization system and service manager that includes features like on-demand starting of daemons, mount and automount point maintenance, snapshot support, and processes tracking using Linux control groups. `systemd` provides a logging daemon and other tools and utilities to help with common system administration tasks.

_In short, `systemd` is a software suite that provides an array of system components for Linux operating systems._

### The Linux Boot Process and `systemd`

Linux requires an initialization system during its boot and startup process. At the end of the boot process, the Linux kernel loads systemd and passes control over to it and the startup process begins. During this step, the kernel initializes the first user space process, the systemd init process with process ID 1, and then goes idle unless called again. systemd prepares the user space and brings the Linux host into an operational state by starting all other processes on the system.

Below is a simplified overview of the entire Linux boot and startup process:

- The system powers up. The BIOS does minimal hardware initialization and hands over control to the boot loader.
- The boot loader calls the kernel.
- The kernel loads an initial RAM disk that loads the system drives and then looks for the root file system.
- Once the kernel is set up, it begins the systemd initialization system.
- systemd takes over and continues to mount the host's file systems and start services.

### `systemd` Units

`systemd` introduces the concept of _systemd units_ and there are several types, such as a _service unit_, _mount unit_, _socket unit_ and _slice unit_. Units are defined in unit configuration files, which include information about the unit type and its behavior.

For most distributions using systemd, unit files are stored in the following directories:

- The `/usr/lib/systemd/user/` directory is the default location where unit files are installed by packages. Unit files in the default directory should not be altered.
- The `/run/systemd/system/` directory is the runtime location for unit files.
- The `/etc/systemd/system/` directory stores unit files that extend a service. This directory will take precedence over unit files located anywhere else in the system.

### `systemd` Tools

The `systemctl` command is a new tool to control the `systemd` system and service. This is the replacement of old `SysV init` system management. Most of modern Linux operating systems are using this new tool. If you are working with CentOS 7, Ubuntu 16.04 or later or Debian 9 system. They have opted `systemd` now.

Use these options to start and stop any service using `systemctl`.

```sh
# systemctl start <name>.service
# systemctl stop <name>.service
```

Use this to reload or restart any service. It also provides reload-or-restart to restart service if reload is not available for any service.

```sh
# systemctl reload <name>.service
# systemctl restart <name>.service
# systemctl reload-or-restart <name>.service
```

Use this to check the status of any service.

```sh
# systemctl status <name>.service
```

Use this to enable or disable any service on your system. The enabled service auto starts on system boot. This is the similar option for `systemd` than `chkconfig` for the `SysV init`.

```sh
# systemctl enable <name>.service
# systemctl disable <name>.service
```

Use this to check if any service is currently active or enabled.

```sh
# systemctl is-active <name>.service
# systemctl is-enabled <name>.service
```

Use this to reload one or more edited `systemd` file.

```sh
# systemctl daemon-reload
```

systemd's `journalctl` tool provides a centralized process and system logging tool. This command allows you to query the systemd journal, which creates and maintains indexed journals from logging information that is pooled from different areas within the system; areas like standard output and standard error of service units, log messages via `syslog`, and kernel log messages. In this way, system administrators can use a single tool to monitor and debug a server.

### Create a simple systemd `.service` file

Create a new `<name>.service` at `/etc/systemd/system` by running `nano /etc/systemd/system/actix.service` and edit the newly created service file by pasting the following lines below.

```
[Unit]
Description=A simple description of the software.
Documentation=https://example.com/
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
User=root
Type=simple
LimitNOFILE=65536
WorkingDirectory=/www/<directory>
ExecStartPre=/root/.cargo/bin/cargo --version
ExecStart=/www/<directory>/target/release/<app.exe>
ExecStop=/bin/kill -s SIGTERM $MAINPID
OOMScoreAdjust=1000
MemoryAccounting=true
MemoryHigh=3G
MemoryMax=4G
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### `[unit]` section

The `[Unit]` section contains generic information about the service. systemd not only manages system services, but also devices, mount points, timer, and other components of the system. The generic term for all these objects in systemd is a unit, and the `[Unit]` section encodes information about it that might be applicable not only to services but also in to the other unit types systemd maintains.

Although section order does not matter to `systemd` when parsing the file, this section is often placed at the top because it provides an overview of the unit. Some common directives that you will find in the `[Unit]` section are:

- `Description=`: This directive can be used to describe the name and basic functionality of the unit. It is returned by various systemd tools, so it is good to set this to something short, specific, and informative.
- `Documentation=`: This directive provides a location for a list of URIs for documentation. These can be either internally available man pages or web accessible URLs. The systemctl status command will expose this information, allowing for easy discoverability.
- `Requires=`: This directive lists any units upon which this unit essentially depends. If the current unit is activated, the units listed here must successfully activate as well, else this unit will fail. These units are started in parallel with the current unit by default.
- `Wants=`: This directive is similar to `Requires=`, but less strict. Systemd will attempt to start any units listed here when this unit is activated. If these units are not found or fail to start, the current unit will continue to function. This is the recommended way to configure most dependency relationships. Again, this implies a parallel activation unless modified by other directives.
- `BindsTo=`: This directive is similar to `Requires=`, but also causes the current unit to stop when the associated unit terminates.
- `Before=`: The units listed in this directive will not be started until the current unit is marked as started if they are activated at the same time. This does not imply a dependency relationship and must be used in conjunction with one of the above directives if this is desired.
- `After=`: The units listed in this directive will be started before starting the current unit. This does not imply a dependency relationship and one must be established through the above directives if this is required.
- `Conflicts=`: This can be used to list units that cannot be run at the same time as the current unit. Starting a unit with this relationship will cause the other units to be stopped.
- `Condition...=`: There are a number of directives that start with Condition which allow the administrator to test certain conditions prior to starting the unit. This can be used to provide a generic unit file that will only be run when on appropriate systems. If the condition is not met, the unit is gracefully skipped.
- `Assert...=`: Similar to the directives that start with Condition, these directives check for different aspects of the running environment to decide whether the unit should activate. However, unlike the Condition directives, a negative result causes a failure with this directive.

### `[Service]` section

The [Service] section which encodes information about the service itself. It contains all those settings that apply only to services, and not the other kinds of units systemd maintains (mount points, devices, timers, ...).

One of the basic things that should be specified within the `[Service]` section is the `Type=` of the service. This categorizes services by their process and daemonizing behavior. This is important because it tells systemd how to correctly manage the service and find out its state.

The `Type=` directive can be one of the following:

- `simple`: The main process of the service is specified in the start line. This is the default if the `Type=` and `Busname=` directives are not set, but the `ExecStart=` is set. Any communication should be handled outside of the unit through a second unit of the appropriate type (like through a `.socket` unit if this unit must communicate using sockets).
- `forking`: This service type is used when the service forks a child process, exiting the parent process almost immediately. This tells systemd that the process is still running even though the parent exited.
- `oneshot`: This type indicates that the process will be short-lived and that systemd should wait for the process to exit before continuing on with other units. This is the default `Type=` and `ExecStart=` are not set. It is used for one-off tasks.
- `dbus`: This indicates that unit will take a name on the D-Bus bus. When this happens, systemd will continue to process the next unit.
- `notify`: This indicates that the service will issue a notification when it has finished starting up. The systemd process will wait for this to happen before proceeding to other units.
- `idle`: This indicates that the service will not be run until all jobs are dispatched.

Some additional directives may be needed when using certain service types. For instance:

- `RemainAfterExit=`: This directive is commonly used with the `oneshot` type. It indicates that the service should be considered active even after the process exits.
- `PIDFile=`: If the service type is marked as 'forking', this directive is used to set the path of the file that should contain the process ID number of the main child that should be monitored.
- `BusName=`: This directive should be set to the D-Bus bus name that the service will attempt to acquire when using the 'dbus' service type.
- `NotifyAccess=`: This specifies access to the socket that should be used to listen for notifications when the 'notify' service type is selected This can be 'none', 'main', or 'all. The default, "none', ignores all status messages. The 'main' option will listen to messages from the main process and the 'all' option will cause all members of the service's control group to be processed.

So far, we have discussed some pre-requisite information, but we haven't actually defined how to manage our services. The directives to do this are:

- `WorkingDirectory=`: This specifies on which directory the service will be launched, same as when you use cd to change a directory when you're working in the shell.
- `ExecStart=`: This specifies the full path and the arguments of the command to be executed to start the process. This may only be specified once (except for 'oneshot' services). If the path to the command is preceded by a dash '-' character, non-zero exit statuses will be accepted without marking the unit activation as failed.
- `ExecStartPre=`: This can be used to provide additional commands that should be executed before the main process is started. This can be used multiple times. Again, commands must specify a full path and they can be preceded by '-' to indicate that the failure of the command will be tolerated.
- `ExecStartPost=`: This has the same exact qualities as `ExecStartPre=` except that it specifies commands that will be run after the main process is started.
- `ExecReload=`: This optional directive indicates the command necessary to reload the configuration of the service if available.
- `ExecStop=`: This indicates the command needed to stop the service. If this is not given, the process will be killed immediately when the service is stopped.
- `ExecStopPost=`: This can be used to specify commands to execute following the stop command.
- `RestartSec=`: If automatically restarting the service is enabled, this specifies the amount of time to wait before attempting to restart the service.
- `Restart=`: This indicates the circumstances under which systemd will attempt to automatically restart the service. This can be set to values like 'always', 'on-success', 'on-failure', 'on-abnormal', 'on-abort', or 'on-watchdog'. These will trigger a restart according to the way that the service was stopped.
- `TimeoutSec=`: This configures the amount of time that systemd will wait when stopping or stopping the service before marking it as failed or forcefully killing it. You can set separate timeouts with `TimeoutStartSec=` and `TimeoutStopSec=` as well.

### `[Socket]` Section

Socket units are very common in systemd configurations because many services implement socket-based activation to provide better parallelization and flexibility. Each socket unit must have a matching service unit that will be activated when the socket receives activity.

By breaking socket control outside of the service itself, sockets can be initialized early and the associated services can often be started in parallel. By default, the socket name will attempt to start the service of the same name upon receiving a connection. When the service is initialized, the socket will be passed to it, allowing it to begin processing any buffered requests.

To specify the actual socket, these directives are common:

- `ListenStream=`: This defines an address for a stream socket which supports sequential, reliable communication. Services that use TCP should use this socket type.
- `ListenDatagram=`: This defines an address for a datagram socket which supports fast, unreliable communication packets. Services that use UDP should set this socket type.
- `ListenSequentialPacket=`: This defines an address for sequential, reliable communication with max length datagrams that preserves message boundaries. This is found most often for Unix sockets.
- `ListenFIFO`: Along with the other listening types, you can also specify a FIFO buffer instead of a socket.

There are more types of listening directives, but the ones above are the most common. Other characteristics of the sockets can be controlled through additional directives:

- `Accept=`: This determines whether an additional instance of the service will be started for each connection. If set to false (the default), one instance will handle all connections.
- `SocketUser=`: With a Unix socket, specifies the owner of the socket. This will be the root user if left unset.
- `SocketGroup=`: With a Unix socket, specifies the group owner of the socket. This will be the root group if neither this or the above are set. If only the `SocketUser=` is set, systemd will try to find a matching group.
- `SocketMode=`: For Unix sockets or FIFO buffers, this sets the permissions on the created entity.
- `Service=`: If the service name does not match the .socket name, the service can be specified with this directive.

### `[Install]` section

The [Install] section encodes information about how the suggested installation should look like, i.e. under which circumstances and by which triggers the service shall be started. In this case we simply say that this service shall be started when the multi-user.target unit is activated. This is a special unit that basically takes the role of the classic `SysV Runlevel 3`. The setting `WantedBy=` has little effect on the daemon during runtime. It is only read by the `systemctl enable` command, which is the recommended way to enable a service in systemd. This command will simply ensure that our little service gets automatically activated as soon as `multi-user.target ` is requested, which it is on all normal boots.

_This section is optional and is used to define the behavior or a unit if it is enabled or disabled._

- `WantedBy=`: The `WantedBy=` directive is the most common way to specify how a unit should be enabled. This directive allows you to specify a dependency relationship in a similar way to the `Wants=` directive does in the `[Unit]` section. The difference is that this directive is included in the ancillary unit allowing the primary unit listed to remain relatively clean. When a unit with this directive is enabled, a directory will be created within `/etc/systemd/system` named after the specified unit with `.wants` appended to the end. Within this, a symbolic link to the current unit will be created, creating the dependency. For instance, if the current unit has `WantedBy=multi-user.target`, a directory called `multi-user.target.wants` will be created within `/etc/systemd/system` (if not already available) and a symbolic link to the current unit will be placed within. Disabling this unit removes the link and removes the dependency relationship.
- `RequiredBy=`: This directive is very similar to the `WantedBy=` directive, but instead specifies a required dependency that will cause the activation to fail if not met. When enabled, a unit with this directive will create a directory ending with `.requires`.
- `Alias=`: This directive allows the unit to be enabled under another name as well. Among other uses, this allows multiple providers of a function to be available, so that related units can look for any provider of the common aliased name.
- `Also=`: This directive allows units to be enabled or disabled as a set. Supporting units that should always be available when this unit is active can be listed here. They will be managed as a group for installation tasks.
- `DefaultInstance=`: For template units (covered later) which can produce unit instances with unpredictable names, this can be used as a fallback value for the name if an appropriate name is not provided.

### Create a socket activated `.service` file

Create a new `<name>.service` and `<name>.socket` at `/etc/systemd/system` by running `nano /etc/systemd/system/actix.service` and edit the newly created service files by pasting the following lines below.

For `<name>.service` file:

```
[Unit]
Description=Actix Web Server.
Documentation=https://actix.rs/
After=syslog.target network.target remote-fs.target nss-lookup.target actix.socket
Requires=actix.socket

[Service]
User=root
Type=simple
LimitNOFILE=65536
WorkingDirectory=/www/<directory>
ExecStartPre=/root/.cargo/bin/cargo --version
ExecStart=/www/<directory>/target/release/<app.exe>
ExecStop=/bin/kill -s SIGTERM $MAINPID
OOMScoreAdjust=1000
MemoryAccounting=true
MemoryHigh=3G
MemoryMax=4G
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

For `<name>.socket` file:

```
[Unit]
Description=Actix Web Server Socket
PartOf=actix.service

[Socket]
ListenStream=0.0.0.0:80
ListenStream=0.0.0.0:443
KeepAlive=true

[Install]
WantedBy=sockets.target
```

Now change `/etc/systemd/system/actix.service` and `/etc/systemd/system/actix.socket` file permission by running:

```bash
# chmod 755 /etc/systemd/system/actix.service
# chmod 755 /etc/systemd/system/actix.socket
```

**`journalctl -f -u app_name.service` for live status view.**

### Configuring `/etc/systemd/system.conf`

The file `/etc/systemd/system.conf` is the global system configuration file that contains the configuration for all the primary system services. This is the file that the service utility uses by default. Custom configuration files with the same format can be specified on a per-service basis.

_Rather then changing this global settings, Changing the systemd service file would be preferable_

For in depth [Understanding Systemd Units and Unit Files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files) visit this link.

## Installing Redis

Redis is an open-source in-memory key-value data store. It can be used as a database, cache and, message broker, and supports various data structures such as Strings, Hashes, Lists, Sets, and more. Redis provides high availability via Redis Sentinel and automatic partitioning across multiple Redis nodes with Redis Cluster.

Visit: [Redis Installation Docs](https://linuxize.com/post/how-to-install-and-configure-redis-on-ubuntu-20-04/)

## Installing MongoDB

MongoDB is a source-available cross-platform document-oriented database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with optional schemas. MongoDB is developed by MongoDB Inc.

Goto : [MongoDB Installation Docs](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) for installation process.

**If error: [mongod.service could not found] occurs after installation, try the following command below.**

`$ sudo systemctl unmask mongodb`

`$ sudo systemctl start mongod`

**NOTE: error: could not compile `mongodb` might occure due to low memory on 1GB instance without sawp memory.**

## Configuration

MongoDB configuration file is located in `/etc/mongod.conf`.

### Limit MongoDB Cache Size

MongoDB, in its default configuration, will use will use the larger of either 256 MB or ½ of (ram – 1 GB) for its cache size. You can limit the MongoDB cache size by adding the `cacheSizeGB` argument to the `/etc/mongod.conf` configuration file

_NOTE: MongoDB uses both its internal cache and the system’s file system cache. So limiting it in one place will cut make it consume more in the other._

Add following lines of code to the `/etc/mongod.conf` to limit the memory consumption:

```yaml
# engine:
wiredTiger:
  engineConfig:
    cacheSizeGB: 1
```

_NOTE: Make sure you copy & paste the code in exactly, otherwise `mongod` will through error!_

## Convert a Standalone `mongod` Instance to a Replica Set

**Remarks:** MongoDB's ACID transactions doesn't work for the standalone mode. Therefore we need at least one replica set to be ACID complaint.

Follow [this link](https://www.mongodb.com/docs/manual/tutorial/convert-standalone-to-replica-set/) on how to convert to Replica Set.

**NOTE:** Make sure to add appropriate `--dbpath` i.e., `/var/lib/mongodb/` and `--bind-ip` i.e., `127.0.0.1`

For the general understanding on Replication follow [this link](https://www.mongodb.com/docs/manual/replication/).

### Quick Setup

Visit [this link](https://hevodata.com/learn/mongodb-replica-set-config/) for more on replica set configuration.

Now stop the MongoDB and make changes to the `.service` file by running `sudo nano /lib/systemd/system/mongod.service`.

Find the following line:

```
ExecStart=/usr/bin/mongod --config /etc/mongod.conf
```

Replace it with the line below:

```
ExecStart=/usr/bin/mongod --port 27017 --dbpath /var/lib/mongodb/ --replSet rs0 --bind_ip 127.0.0.1 --config /etc/mongod.conf
```

And run: `sudo systemctl daemon-reload && sudo systemctl start mongod`

**NOTE:** In case of Error: 100; run `sudo chown -R mongodb:mongodb /var/lib/mongodb/*` and restart MongoDB. If that doesn't solve the issue then run `sudo mongod` and see the error message. if it's the `/tmp/mongodb-*.sock` file the run the following command.

```bash
sudo chown mongodb:mongodb /tmp/mongodb-*.sock
sudo chmod 755 /tmp/mongodb-*.sock
```

To enable **MongoDB Replica Set** goto `mongosh` and run:

```
rs.initiate()
```

## Common Queries

List all databases:

```
show databases
```

Switch to a database:

```
use <db_name>
```

Show collections on a selected database:

```
show collections
```

Documents count based on a query:

```
db.<collection>.find({"<key>": "<value>"}).count()
```

For more accurate counts:

```
db.<collection>.countDocuments({"<key>": "<value>"}).count()
```

Show all documents of a collection:

```
db.<collection>.find()
```

Update single documents specified field based on query:

```
db.<collection>.update({"<key>": "<value>"}, {"$set": {"<key>": "<value>"}})
```

Update many documents specified field based on query:

```
db.<collection>.updateMany({"<key>": "<value>"}, {"$set": {"<key>": "<value>"}})
```

## Indexes

To create an index for a particular collection run:

```
db.<collection-name>.createIndex({ <uuid>: 1})
```

Here `1` means ascending and `-1` means descending order.

To view available index on a collection run:

```
db.<collection-name>.getIndexes()
```

## Free up Spaces

By default MongoDB's WireTiger engine hold the space for the deleted documents to use in future. To release these empty space from a particular collection run:

```
db.runCommand({compact: <collection-name>})
```

## Import and Export a MongoDB Database for a Collection

```
mongoimport --db <database_name> --collection <name> --file data.json
mongoexport --db <database_name> --collection <name> --out data.json
```

## Import and Export an Entire MongoDB Database

To create backup of database in MongoDB, you should use mongodump command. This command will dump the entire data of your server into the dump directory. `dump` directory will be created on the current working directory. There are many options available by which you can limit the amount of data or create backup of your remote server.

To create a backup run:

`$ mongodump`

_You don't have to log in to `mongo` shell for this command. `mongodump` is a binary comes with mongo installation._

To restore a backup run:

`$ mongorestore`

**NOTE: Check the `dump` directory timeStamp before restore to make sure you are restoring the right backup.**

**IMPORTANT: Not tested yet. Perhaps this command shoud run on the directory where `dump` folder exists or inside the `dump` directory.**
