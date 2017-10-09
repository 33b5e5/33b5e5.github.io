---
layout: default
title: Void Linux
date: 2017-10-09 15:22:23 -0700
---

I've been testing <a href="https://www.voidlinux.eu/" target="_blank">Void Linux</a> recently, and thought I'd take a few minutes to write about my experience. Void is an independent, open source Linux distribution with a unique package manager and an init system written from scratch. The Github repo is <a href="https://github.com/voidlinux" target="_blank">here</a>.

Void uses a "rolling release" strategy; there are no specific releases or versions. You can update packages daily or whenever you chose, and you'll always get the latest code. They use a <a href="http://build.voidlinux.eu/" target="_blank">continuous build system</a> behind the scenes to produce packages as soon as changes are pushed.

Void's new package manager is called *xbps*. Although I had no trouble picking up the syntax, and it seemed to handle dependencies and do everything else you'd expect a modern package manager to do, I didn't find it particularly notable or exceptional. They've outlined some of its purported benefits <a href="https://github.com/voidlinux/xbps/blob/master/README.md" target="_blank">here</a>.

Where Void Linux really shines, and by far my favorite thing about it, is the init system, *runit* ("run it"). You can read more about runit <a href="https://www.voidlinux.eu/usage/runit/" target="_blank">here</a>. Sometimes a screencap is worth a thousand words:

![void1.png](https://raw.githubusercontent.com/33b5e5/33b5e5.github.io/master/_images/void1.png)

That's the running process tree from my Void Linux system. That's it! That's all that is running on my freshly-installed system. Admire the simplicity. I could explain what every process is doing there. Try that on a fresh install of Ubuntu!

As a result, the OS itself has a remarkably small footprint:

![void2.png](https://raw.githubusercontent.com/33b5e5/33b5e5.github.io/master/_images/void2.png)

The system is only using 33 MB of RAM. Incredible!

The underlying approach to managing services is genius. Basically, it's just symlinks. If you look at the directory /var/service, you can see which services are enabled. If there's a symlink there to an existing service in /etc/sv, that service will start at boot (and continue running under the management of runit). Need to disable a service? Just delete the symlink!

![void3.png](https://raw.githubusercontent.com/33b5e5/33b5e5.github.io/master/_images/void3.png)

Let's take a look at a service configuration in /etc/sv. Here I've created a new service configuration for ntpd (network time protocol). It's basically one line of Bash. It execs ntpd as the ntpd user. That's it.

![void4.png](https://raw.githubusercontent.com/33b5e5/33b5e5.github.io/master/_images/void4.png)

With that in place, all you need to do is create a symlink in /var/service, and you're done.

I'll definitely be keeping an eye on Void Linux. I absolutely love runit. It's such a nice anecdote to the over-engineered complexity of systemd, the init system used my the majority of popular distributions.

Unfortunately there are some chicken-and-egg issues preventing me from using Void in production systems. Namely, it's not available as an install target on any of the hosting providers I use. I had to create a custom ISO image in order to install it on a VPS (custom ISOs are a nice feature over at <a href="https://www.vultr.com/" target="_blank">Vultr</a>, my favorite VPS host). Void also suffers from a lack of adoption (and a resulting lack of community support) at this early stage. Try Googling for solutions to problems with Void, and you'll probably be disappointed. Nonetheless, these problems should wane as time goes on and more people start to use Void.

I'd encourage anyone interested in a new approach to Linux to <a href="https://www.voidlinux.eu/download/" target="_blank">check it out</a>!
