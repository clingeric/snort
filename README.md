# Snort
IS 560 Snort Walkthrough

## Setting up your VM

For this tutorial, I'm going to use Google Cloud Platform (GCP), but feel free to use the cloud provider you're most familiar with. 

First, create a new VM running Debian 10 (Buster)

Allow access to ports 20, 80, and 443

Leave the settings on whatever you'd like. Snort doesn't need much to run 

*I recommend staying within the free tier of whatever cloud provider you choose.* 

## Setting up Snort

Login to your VM and update apt

```shell
$ sudo apt update
```

We now need to find the name of our network adapter

```shell
$ ip a
```

Mine appears as this:

```shell
<adapter name>: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 42:01:0a:8a:00:07 brd ff:ff:ff:ff:ff:ff
    inet <ip address>/32 brd <ip address> scope global dynamic ens4
       valid_lft 3413sec preferred_lft 3413sec
    inet6 fe80::4001:aff:fe8a:7/64 scope link 
       valid_lft forever preferred_lft forever
```

*remember the name of the adapter and the IP address for later!*

```shell
$ sudo apt install snort
```

*enter the adapter name when prompted. Just hit enter when it asks about the subnet*

Test to see if Snort was installed correctly:
```shell
$ sudo snort -V
```

Modify the Snort rules to add a rule that alerts when an ICMP packet is sent:

```shell
$ sudo nano /etc/snort/rules/local.rules
```

Add: 

```shell
alert icmp any any -> any any (msg: "ALERT"; sid: 100001)
```

Save the file and run Snort as root:

```shell
sudo snort –A console –c /etc/snort/snort.conf –i <adapter name>
```

Open a terminal and ping your VM:

```shell
$ ping <ip address>
```

Notice the **ALERT** messages coming from Snort on your VM

## Attacking Snort

Use Powershell to overwhelm Snort

```powershell
Test-Connection -ComputerName 35.185.208.132 -Count 50000 -AsJob | Wait-Job | Receive-Job
```

Notice that *hopefully* Snort has dropped quite a few packets. This means that rouge packets have a greater chance to get through to the target machine.
