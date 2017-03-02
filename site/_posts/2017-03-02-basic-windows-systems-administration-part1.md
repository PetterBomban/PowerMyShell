---
layout: post
title: "Basic Windows Systems Administration Part 1"
---

This guide won't provide any in-depth tutorials for how to do things. Instead, it will provide you with a list of essentials to point you in the right direction. 

I recommend using Windows Server 2016, but you can follow everything with Server 2012 R2 as well.

*I am not a very experienced IT-person in general, so please send corrections / improvement tips etc. to PoSh@bomban.no.*

## Step 1 - Hyper-V

You need to set up a Hyper-V server for virtualization. You can either set it up on your computer, or on a dedicated server (recommended). To set it up on your own computer, go to "Turn Windows features on or off" and select Hyper-V

If you want to use a dedicated server, you first need to install Windows Server on it. You can use either Hyper-V Free Server, Nano Server, Core or the regular server with a GUI. I recommend either Hyper-V Free Server or Nano Server.

- [Nano Server](https://blogs.msdn.microsoft.com/joscot/2016/12/24/creating-a-hyper-v-host-with-nano-server-part1/)

- [Hyper-V Server](https://hyperv.veeam.com/blog/how-to-install-hyper-v-core-step-by-step-guide/)

- [Regular Server (GUI)](https://technet.microsoft.com/en-us/library/hh846766(v=ws.11).aspx)

Tip: You can connect to a Hyper-V server with the Hyper-V Client on your own computer.

## Step 2 - Virtual Machines

In this part, I'll list a couple of servers for you to set up.

[Creating a VM in Hyper-V](https://technet.microsoft.com/en-us/library/hh846766(v=ws.11).aspx#Anchor_2)

| Server             | Server type             | Details                                                                                                                                                  |
|--------------------|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2x  | Domain Controller / DNS | One primary DC, one secondary (with sync). Create AD structure, users etc.                                                                               |
| 1-2x    | Clients (win10)         | Set up one or two clients and join it to the domain.                                                                                                     |
| 1x  | File Server             | File server to store stuff. Set up at least one "Backup" share.                                                                                          |
| 1x  | Veeam B&R Free          | Veeam is backup-software. It connects to Hyper-V and backs up your VM's.  Send backups to the share created above. (Tip: Veeam supports PowerShell 100%) |
| 1x  | DHCP (for clients)      | Set up a /24 scope for clients. Exclude a couple of IP's from the pool, use these for servers.                                                           |
| 1x  | Deployment              | Use either: MDT, for more advanced options (for example installing applications while deploying) - or - WDS, for simple image-deployment                 |

## Step 3 - Tasks

### Domain controllers
---
* Set up replication between the two DC's. 
* Configure DNS with the primary DC as the primary DNS-server. Set up sync between these.
* Create an AD-structure.
* Create users and groups as you see fit
* Configure homefolders and public shares for the users (The shares are put on the file server)

**Bonus tasks**
* Try to use PowerShell for most of the above
* Set up a GPO that changes the homepage of Internet Explorer
* Set up a GPO that deploys an application (PuTTY, Adobe Reader etc.)

### File Server
---
* Set up a backup-share (For Veeam)
* Set up a public share for all users
* Set up a share for homefolders

**Bonus tasks**
* Look into shadow copies
* Check out PowerShell scripts for monitoring shares for crypto-viruses

### Veeam (Backup server)
---
* Install Veeam B&R free edition
* Try to back up one of your clients, and then restore from backup

**Bonus tasks**
* Check out [LINK](https://github.com/PetterBomban/VeeamBackup) for automating backup-jobs

Tip:
Change the bottom of the script above to something like this:
{% highlight powershell %}
$Params = @{
    ServerTypes = *
    Skip = "You-Backup-Server-Name-In-HyperV-Here"
    Destination = "BACKUP-SHARE"
    Compression = 6
    KeepBackupsFor = 4
    DisableQuiesce = $True
}
Start-VeeamZip @Params
{% endhighlight %}

### DHCP
---
* Set up a DHCP scope that configures IPv4 for clients
* Exclude for example 20 IP-addresses from the pool for your servers

**Bonus tasks**
* Can you do this in PowerShell?

### Deployment
---
Here I gave you two choices, depending on how advanced you want to get. I wholeheartedly recommend MDT, as this will save you a lot of time with deployments (but the learning curve is a bit higher compared to just WDS.)

* [WDS Guide](http://protechgurus.com/install-configure-wds-windows-server-2016/)
* [MDT Guide (Part1)](http://www.technig.com/install-adk-mdt-in-windows-server-2016/)
* [MDT Guide (Part2)](http://www.technig.com/deploy-windows-10-using-mdt/)

*To be continued*
