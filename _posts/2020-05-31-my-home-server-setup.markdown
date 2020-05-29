---
layout: post
title:  "My home server setup"
date:   2020-05-31 9:00:00 -0400
categories: home-server
---
With the pandemic lingering, I took the time to *finally* make a home server setup to store my files and do other server-related things (more on that later).

Ideally, I would've made a desktop computer with easily accessible drive bays, tons of memory, etc. However, I had an existing machine to use, and I didn't have a lot of money to throw into a new computer build. I was also very impatient and wanted to throw something together immediately.

There are a plethora of guides online on making a home server (that I'll reference), and I won't reinvent the wheel here. Instead, I'll spend some time just describing my setup in case later posts depend on my setup's pecularities.

In later posts, I'll describe how I use my server for purposes other than file storage (e.g., hosting virtual machines, etc.), but I felt it awkward to jump straight into those topics without discussing my setup first.

So let's discuss my setup:

# Summary

## Motivations (or why I started a home server anyway)
- I had an old Acer laptop + charger (circa 2015, so pretty recent) that was laying around.
- I had a lot of spare hard drives and enclosures that I wasn't using.
- My internet is slow (<1 mb upload), so using a cloud service (e.g., OneDrive, Google Drive) is possible, but inconvenient for bigger files.
- I wanted to store my photos and other big files and didn't want to pay for additional storage in such cloud services.
- I wanted to learn more about networking.

## tl;dr (or key takeaways from my home server setup)
- Creating a home server today is *easy*. Some technical knowledge is helpful, but basically all configuration can be done without breaking out a terminal.
- Creating a home server doesn't require expensive hardware. I'm able to host a virtual machine, Plex server, and a few other goodies on a laptop with 8 GB of RAM.

## My home server setup in a nutshell
- All run on an Acer 15.6 laptop with the following specs:
  - Haswell i5
  - 128 GB SSD to host the OS
  - 8 GB DDR3
  - 2x 1TB drives connected through USB 2
- Freenas OS
- File access is done through SMB shares (client computers that connect to the server all run Windows) and through SCP/SFTP
- Administrative access is done through the web GUI and `ssh`

# Full text
## Creating the setup
I won't drone on about installing and configuring Freenas, since a lot of is pretty easy and there are tons of existing guides already.

I booted the Freenas installer ISO off a USB drive. The installer is pretty self-explanatory.

**NOTE**: Freenas wants to use an entire drive for the OS (e.g., doing a dual-boot is inconvenient). This drive cannot be used anything else but holding the OS (like file storage). When using Freenas, you'll likely want multiple drives so that you an use extra drives for file storage.

Configuration can be done through the web GUI. When you first boot Freenas (after installation), it'll display its IP address (so it's a good idea to connect your server to a display so that you can find this).

When installing Freenas, you'll set a password for `root`. After installation, you'll want to create an account for yourself and other users. You can allow sudo access for new accounts, though I made my user account not have such privileges.

### Storage
Freenas will need to be told how to set up its storage. I forget which guide I used exactly for setup, but here's [a pretty good one](https://www.tested.com/tech/500455-building-home-server-using-freenas/). It's a little old, but it still is mostly usable.

Here's quick rundown of how storage configuration is done.

For file-sharing you'll want to go to `Storage -> Pools`.

A pool is simply a collection of disks (or a single disk) that is grouped together for storage. If you're using multiple disks, for example, you could configure them in RAID for redunancy. You could also forgo RAID to maximize the space available (e.g., JBOD, though this is probably not a good idea if you have many disks).

After creating a pool, you'll want to create datasets, which you can think of as folders within a pool. It's a good idea to have multiple datasets for different kinds of data. For example, if you plan on using plugins, you can specify the dataset(s) each plugin will have access to. You may want to have a 'Movies' dataset that Plex has access to so that Plex can access your movies (but nothing else).

On the topic of plugins, there is often some setup involved when using plugins. You can easily install a lot of plugins through the Web GUI (through the `Plugins` menu item), but for a plugin to access datasets, you'll want to check out [this guide](https://www.ixsystems.com/community/threads/how-to-giving-plugins-write-permissions-to-your-data.27273/).

## Fixing the IP of the host machine
If your network is running a DHCP server (usually through your router), then your server's IP can change periodically (especially after server restarts). You may want to configure your server so that its IP is fixed. Doing so makes it more convenient in connecting to your server on the local network since you'll only need to remember one IP address (rather than constantly checking if has changed). Fixing your server's IP is also necessary if you wish to remotely access your server (e.g., outside of your home network). I discuss remote access in the following section.

There are 2 broad ways of fixing your server's IP: through your server itself or (possibly) through your DHCP server (router). The first method causes your server to request a specific IP from your router. The second method ties your server's MAC address your DCHP server (router). Thus, whenever your server connects to your router, your router will see that your server has a known MAC address and thus assign a specific IP address for the MAC address.

I chose the second option (using my router), but either method should work fine.

This [helpful guide](https://pureinfotech.com/set-static-ip-address-freenas/) shows how to fix your server's IP through the server itself (including through the web GUI).

In my network, my DCHP server resides on my router. In my router's web interface, there is an option in the LAN settings (`Advanced Setup -> LAN`) that enabled static IP leasing. You may need to poke around your router to find this option.

## Remote Access
Everything to configure your server can be done through the web GUI. You can access the web GUI through any machine connected to your home network, though without additional configuration, you won't be able to access your server anywhere else.

I used the [following guide](https://www.ixsystems.com/community/threads/how-to-how-to-access-your-freenas-server-remotely-and-securely.27376/) to set up remote access.

For Windows users, the bit about using Putty isn't completely necessary [since openssh is available for Windows 10](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse). However, using Putty is completely fine, so if you are unsure, you can follow the remote access guide as written.

## Future steps and other notes
In a later blog post, I'll discuss how I used this server to host a Linux virtual machine (VM) that I use for programming. My main laptop has Windows, and while I could use other solutions for Linux programming (e.g., dual boot or [WSL](https://docs.microsoft.com/en-us/windows/wsl/about)), making the VM was easy, fun to do, and a good exercise in remote development (which may become [more prevalent](https://www.theatlantic.com/health/archive/2020/05/work-from-home-pandemic/611098/) in the future).

I taped the webcam on my server laptop, since the machine is remotely accessible.

I want to enable my server to be able to serve installation ISOs over the network. My current plan is to use a Debian VM running the [FOG Project](https://fogproject.org/). I haven't gotten too far on that project.

I currently store my [keepass](https://keepass.info/) password database on my server. A better solution might be using more server-centric solutions (like [passbolt](https://www.passbolt.com/)). I'll discuss my exact password setup in detail in a future blog post.