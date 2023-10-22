---
title: Samba
date created: 2023-10-22 10.00
tags:
  - FOSS
  - networking
  - linux
---
Samba provides inter-computer file sharing and printing services over a network. It uses the Server Message Block and Common Internet File System (SMB/CIFS) protocol, and as a result, the services created by Samba can be used even on Linux, MacOS, and Windows clients.

# Installation

## Server

If you want to set up a server using Samba, you can follow the steps below:

First of all, install the `samba` package. This package is available in the repositories of most distributions.

Before starting the configuration, I'll provide the general steps for using Samba normally.

First, you need to enable or start the `smb.service` (in distributions that use systemd) for Samba to work. For that:

```sh-session
# systemctl start smb.service # For starting the smb.service immediately
# systemctl enable smb.service # To enable automatic startup at boot
```

You should start smb.service after configuring it.


Second, if you are using a firewall, you need to allow Samba. This is how to do it in ufw and firewalld:

```sh-session
# ufw allow CIFS
# ufw reload

# firewall-cmd --permanent --add-service={samba,samba-client,samba-dc}
# firewall-cmd --reload
```


Third, if your distribution uses SELinux, do the following to allow client access:

```sh-session
# setsebool -P samba_export_all_ro=1 samba_export_all_rw=1
```


And finally, if you want to make your device discoverable, you can use Avahi. This way, the device can be discovered by other devices and easily connected to. Otherwise, you'd need to connect to the device using its IP address or domain.

Install the `avahi` package and enable the service. For distributions that use systemd, run the following command:

```sh-session
# systemctl enable avahi-daemon.service
```

Unfortunately, this doesn't work for Windows because Windows doesn't support Zeroconf yet. For more information and a solution, you can check [this](https://www.truenas.com/community/resources/how-to-kill-off-smb1-netbios-wins-and-still-have-windows-network-neighbourhood-better-than-ever.106/) page. 

In short, what's needed is to use [`wsdd`](https://github.com/christgau/wsdd) or [`wsdd2`](https://github.com/Netgear/wsdd2). Some distributions have `wsdd` in their repositories. So, you can install it from there and enable `wsdd.service`. This should be resolved smoothly this way.

### Configuration

Now, we can configure Samba with [`smb.conf`](https://man.archlinux.org/man/smb.conf.5).  First, open the configuration file with an editor.

```sh-session
# nano /etc/samba/smb.conf
```

#### Basic File Sharing

This configuration file is for basic file sharing. In later sections, I will provide an advanced configuration file, including printing.

```
[shared_folder]
   comment = Shared Folder
   path = /path/to/shared/folder
   read only = no
   guest ok = yes

```


- `[shared_folder]`: This is the section header and represents the name of the shared folder as it will appear when clients browse the network. You can replace it with anything you desire.

- `comment`: It's a brief description or comment for the shared folder, providing some context or information about the folder.

- `path`: This option specifies the local file system path to the directory that you want to share. Replace `/path/to/shared/folder` with the actual path to the folder you want to share. You can create it with `mkdir` command or something in a place you have required permissions.
   
- `read only`: If set to "no," it allows both read and write access to the shared folder.
   
- `guest ok`: When set to "yes," it allows guest (unauthenticated) access to the shared folder, meaning users don't need to provide a username and password to access the folder. If you set it to "no," users will need to authenticate to access the folder.

In this configuration we allowed guest acess so we don't need a user. Since I will explain user authentication in the following sections, I won't cover username authentication at this stage. Configuring it can be a bit more complex.


#### Printing

Before starting, you need to add a printer on the server that runs Samba. For that, you can use CUPS (which you'll need to install). I won't go into printer driver installation or requirements here, as they can vary. However, there is an article about adding the printer after installing the driver [here](https://distro.ibiblio.org/smeserver/contribs/rvandenaker/testing/smeserver-cups/documentation/howtos/cups-add-printer.html)

In Samba, to enable printing, you should have a configuration file like this:

```
[global]
printing = CUPS

[printers]
path = /var/tmp
guest ok = yes
printable = yes
```

* `[global]` Section: Contains global settings that apply server-wide and affect various aspects of the Samba server. In this configuration, it specifies that the server uses CUPS for printing services and configures other global printing options. 
	* `printing`: This parameter specifies which backend will be used for printing services. In this case, we'll use Common Unix Printing System (CUPS).
* `[printers]` Section: This is a special section in Samba that automatically shares locally installed printers on the server without the need for individual configurations.
	* `path`: This parameter specifies the directory where print job files are stored. Typically the path specified is that of a world-writeable spool directory with the sticky bit set on it.
	* `guest ok = yes`: This allows guest access to the printers. Guest users can print without requiring authentication.
	* `printable`: This parameter indicates that the shared printer is capable of receiving print jobs. It should be set to "yes" to enable printing.

By doing this, we have configured printers on Samba in a straightforward manner.


#### User Authentication in Samba

In this section, I'll attempt to explain how you can manage user access to Samba in a straightforward manner.

```
# Global Settings
[global]
   server string = Samba Server
   security = user
   map to guest = Bad User
   encrypt passwords = yes
   passdb backend = tdbsam
   printing = CUPS

# File Share Configuration
[shared_folder]
   comment = Shared Folder
   path = /path/to/shared/folder
   read only = no
   guest ok = no
   valid users = +ambausers

# Printer Share Configuration
[printers]
	path = /var/tmp
	printable = yes
	valid users = +sambausers
```

I will explain new parameters that we haven't seen in previous sections:

* `[global]` Section
	- `server string`: A description of the server that clients will see when connecting to it.
	* `security`: It specifies the security mode used for authentication. "user" means user-level security. With user-level security a client must first "log-on" with a valid username and password.
	* `encrypt passwords`: Enables password encryption.
	* `passdb backend`: Specifies the password database backend, "tdbsam" is used.

* `[shared_folder]` Section and `[printers]` Section
	* `valid users`: Lists the valid users who can access this share. If a name is prefixed with @, &, or + symbols, it is considered as a group. The differences between these symbols are explained on [the manpage](https://man.archlinux.org/man/smb.conf.5#EXPLANATION_OF_EACH_PARAMETER) under the "invalid users" parameter. In this case, members of the "sambausers" group can only access the printing and file sharing service.

With a configuration file like this, we can achieve at least a sufficient level of security, especially in a home environment.

However, to use a configuration file like this, a few steps are required. First, let's create the necessary group:

```sh-session
# groupadd sambausers
```

Now we create users and add users to the new group with the command:
```sh-session
# useradd -G sambausers USER
```
*Where USER is the username to create and add to the group.*

> [!note] Note
> Make sure that the created users or groups have the appropriate permissions on the shared folder. You can accomplish this with commands like `chown`, `chgrp`, or `chmod`.

Finally, we must add the users to Samba. This is done with the `smbpasswd` command like so:
```sh-session
# smbpasswd -a USER # Adds the user to the Samba server without enabling them.
# smbpasswd -e USER # Enables a previously-added user.
```
*You will be prompted to create a new Samba password for the user.*

And that's it. Everything is ready now. All you need to do is restart the Samba service or start it if it hasn't started yet. In addition, if you've made changes to the configuration, you can test the configuration file with the `testparm` command.


## Client

To access Samba, you can use various tools. I'll show some of them. First, to access from command line, install `smbclient` package.

`smbclient` uses the following format to access Samba shares:

```shell
smbclient //your_samba_hostname_or_server_ip/shared_folder -U username
```

> [!note]
> Running `$ smbtree -N` will show a tree diagram of all the shares.

After running the `smbclient` command, you will be prompted for the Samba password and logged into a command line interface reminiscent of the FTP text interface. This interface is most useful for testing usernames and passwords and read-write access. For example, you can create a directory and list its contents as follows:

```shell
smb: \> mkdir test
smb: \> ls
```

Managing data in a share is often easier with a GUI tool. For that work, we can use KDE's Dolphin or GNOME's Nautlius. These are just a few examples.


# Conclusion

In conclusion, Samba stands as a versatile and powerful solution for seamlessly integrating file and printer sharing across a network, connecting various operating systems, and facilitating collaborative work environments. This document has provided a comprehensive guide on how to harness the capabilities of Samba, covering essential configuration parameters, access control and user authentication.

With Samba, you have the tools to foster efficient collaboration in both home and business environments, with the added benefit of cross-platform compatibility. Whether you're looking to share files, printers, or both, Samba simplifies the process and provides robust security measures to safeguard your data.


# See Also

- https://wiki.archlinux.org/title/samba
- https://man.archlinux.org/man/smb.conf.5
- https://wiki.samba.org/index.php/Main_Page