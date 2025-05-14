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