---
deprecated: false
author:
  name: Alex Fornuto
  email: docs@linode.com
description: 'Information on the Network Helper option.'
keywords: 'network, networking, network helper, ip, ip address, static ip,'
license: '[CC BY-ND 3.0](http://creativecommons.org/licenses/by-nd/3.0/us/)'
modified: Monday, December 8, 2014
modified_by:
  name: Alex Fornuto
published: ''
title: Network Helper - BETA
---

Network Helper automatically deposits a static networking configuration in to your Linode at boot. Thanks to Network Helper, you don't have to worry about altering your network configuration when you:

 - Deploy a Linode
 - Add a public or private IPv4 address
 - Restore from a backup
 - Deploy from an Linode Images template
 - Migrate your Linode to a new datacenter
 - Clone from another Linode


{: .note }
> Network Helper does not affect IPv6 networking. Please see our [Native IPv6 Networking](/docs/networking/native-ipv6-networking) for more information on IPv6.

## What Does It Do?

On enabled profiles, the Network Helper works by detecting which distribution is booting, and modifies the appropriate configuration files to assign the IPv4 address statically. [Jump to](#what-files-are-affected) the list of distribution-specific files.

Below is an example network configuration file for a Debian 7 Linode with Network Helper enabled:

{: .file}
/etc/network/interfaces
:   ~~~
    # Generated by Linode Network Helper
    # Thu Dec  4 20:24:07 2014 UTC
    #
    # This file is automatically generated on each boot with your Linode's
    # current network configuration. If you need to modify this file, please
    # first disable the 'Auto-configure Networking' setting within your Linode's
    # configuration profile:
    #  - https://manager.linode.com/linodes/config/web1?id=123456
    #
    # For more information on Network Helper:
    #  - https://www.linode.com/docs/platform/network-helper
    #
    # A backup of the previous config is at /etc/network/.interfaces.linode-last
    # A backup of the original config is at /etc/network/.interfaces.linode-orig
    #
    # /etc/network/interfaces

    auto lo
    iface lo inet loopback

    auto eth0
    allow-hotplug eth0

    iface eth0 inet6 auto

    iface eth0 inet static
        address 198.74.53.231/24
        gateway 198.74.53.1
        up   ip addr add 12.34.56.78/24 dev eth0 label eth0:1
        down ip addr del 12.34.56.78/24 dev eth0 label eth0:1
        up   ip addr add 192.168.138.44/17 dev eth0 label eth0:2
        down ip addr del 192.168.138.44/17 dev eth0 label eth0:2
    ~~~

{: .caution}
>If you create an advanced configuration, Network Helper will undo those changes at the next boot. For advanced configurations, turn off Network Helper.

## Turn Network Helper On for Individual Configuration Profiles.

Even with Network Helper's default behavior set to **OFF**, you can enable Network Helper on specific configuration profiles.

1.  Go to your Linode's Dashboard, and under Configuration Profiles click **Edit** for the profile you want to adjust:

    [![The Edit link for a Configuration Profile](/docs/assets/linode-dashboard-hilighted_small.png)](/docs/assets/linode-dashboard-hilighted.png)

2.  Under the Filesystem/Boot Helpers section, change the **Auto-Configure Networking** option to Yes:


    [![The Auto-configure Networking option](/docs/assets/network-helper-hilighted_small.png)](/docs/assets/network-helper-hilighted.png)

3. Click on **Save Changes**.

### Failure to Run

If Network Helper is unable to determine the operating system during boot, it will not attempt to write any new configuration files. When this happens, Network Helper will let you know in the Host Job Queue:

[![Network Helper Failure Message](/docs/assets/network-helper-failure_small.png)](/docs/assets/network-helper-failure.png)

Similarly, if you boot an unsupported older distribution while Network Helper is enabled, you'll see a warning in the Host Job Queue:

[![Network Helper Failure Message](/docs/assets/network-helper-unsupported_small.png)](/docs/assets/network-helper-unsupported.png)

## What Files are Affected

You will retain 3 versions of each file modified by Network Helper after each boot. Consider Debian for this example. In addition to the `/etc/network/interfaces` file, Network Helper will create:

- A copy of the file as the distribution provided it: `.interfaces.linode-orig`.

- A copy of the file from the previous boot: `.interfaces.linode-last`. If you made manual changes to the file during the previous boot, you'll find it saved here.

If you need to restore manual changes made during a previous reboot, use the following command (replacing `/etc/network/interfaces` with the files for your specific distribution):

        mv /etc/network/.interfaces.linode-last /etc/network/interfaces

If you'd like to know what files Network Helper modifies specifically, scroll down to your preferred distribution.

###Debian & Ubuntu

Network helper configures `/etc/network/interfaces` & `/etc/resolv.conf`.

### CentOS & Fedora

Network Helper configures `/etc/sysconfig/network-scripts/ifcfg-eth0` & `/etc/resolv.conf`.

### Arch

Network Helper configures `/etc/systemd/network/05-eth0.network` & `/etc/resolv.conf`.

### Gentoo

Network Helper configures `/etc/conf.d/net` & `/etc/resolv.conf`.

### OpenSUSE

Network Helper configures `/etc/sysconfig/network/ifcfg-eth0`, `/etc/sysconfig/network/routes` & `/etc/resolv.conf`.

###Slackware

Network Helper configures `/etc/rc.d/rc.inet1.conf` & `/etc/resolv.conf`.

##Turn Network Helper On for all New Configuration Profiles

Once Network Helper moves out of beta it will be enabled on all new configuration profiles by default. Until then, to set this behavior as default follow the steps below.

1.  From the Linode Manager, click on the **Account** tab:

    ![The Account tab in the Linode Manager](/docs/assets/account-tab.png)

2.  Click on the **Account Settings** tab. Under The Network Helper section, Change the Default Behavior to **ON**:

    [![The Network Helper Default Behavior option](/docs/assets/account-settings_small.png)](/docs/assets/account-settings.png)

3. Click the **Save** button.