---
Title: Using Kali Linux in WSL - Port Forwarding using PowerShell
Summary:  A quick reference for forwarding network traffic intended for the Kali Linux WSL environment
Author: Andy Jordan
Category: Reference
---
[/project-sideshow/PowerShell-PortForwarding-to-WSL](https://github.com/brookline-lab/project-sideshow/tree/main/PowerShell-PortForwarding-to-WSL)

A recent exercise on [TryHackMe](tryhackme.com) had me scratching my head when it came time to set up a successful reverse shell.  My Kali Linux distro, running in WSL, wasn't able to receive a connection. The exercise recommended using `tcpdump` to test this connection but it wasn't working.  

I orgininally set off to find the Windows equivalent to `tcpdump` and landed on an interesting tool named `pktmon` along with a handy [guide](https://www.bleepingcomputer.com/news/microsoft/windows-10-quietly-got-a-built-in-network-sniffer-how-to-use/) to go with it.  But I felt this was going to add just another entry to a long list of tools that I can never remember the syntax for and ultimately wasn't really going to solve my problem.

Instead, I looked into how to do port forwarding in Windows.  Knowing how to do this should be useful for other future challenges.  This reference assumes the reader already has a Linux distribution up and running in WSL.

First we will need the IP address from Kali (or whatever distro is installed), which will probably be on the eth0 interface.  

```bash
# First make sure WSL is running, otherwise start it with WSL
WSL --list --running
# ifconfig can either be run from the BASH command line, or if you exit back to 
# PowerShell, it can be invoked from there:
PS C:\> WSL ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.31.1.242  netmask 255.255.240.0  broadcast 172.31.127.255
        inet6 fe80::215:5dff:fe73:1f57  prefixlen 64  scopeid 0x20<link>
        ether 00:15:5d:73:1f:57  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6  bytes 516 (516.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

The command `netsh interface portproxy show all` will show any active port forwarding:
```powershell
# netsh will return nothing if there are no ports being forwarded 
PS C:\> netsh interface portproxy show all
PS C:\>
```

The following PowerShell commands can be implemented to call the `netsh` utility with our own variables:

```powershell
# $port = Port number to forward
# $target_ip = the IP address of the WSL environment
Invoke-Expression "netsh interface portproxy add v4tov4 listenport=$port connectport=$port connectaddress=$target_ip";
Invoke-Expression "netsh advfirewall firewall add rule name=$port dir=in action=allow protocol=TCP localport=$port";
```

Once `netsh` has been passed our variables, running the show all command again will list them

```powershell
PS C:\> netsh interface portproxy show all
Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
*               8080        172.31.1.242    8080
*               2202        172.31.1.242    2202
*               9999        172.31.1.242    9999
```

A full script is available at the repository link at the top of the page.