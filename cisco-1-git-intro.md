# My First Git Lab

This is my first document stored in Git for Cisco one.

# Cisco Networking Setup Guide

Setup notes for CCNA: Introduction to Networks

Modules 1 (Networking Today) and 3 (Protocols and Models) are mostly ideas like network types, TCP/IP, and the OSI model, and they are explained in the Background sections of this guide. The commands all come from module 2 (Basic Switch and End Device Configuration). Follow the steps in order and you end up with a switch that is named, locked down, addressed, reachable over SSH, and saved.

## Table of Contents

- [Overview](#overview)
- [Background: Module 1 (Networking Today)](#background-module-1-networking-today)
- [Background: Module 2 (Basic Switch and End Device Configuration)](#background-module-2-basic-switch-and-end-device-configuration)
- [Step 1: Basic switch configuration](#step-1-basic-switch-configuration)
- [Step 2: IP addressing](#step-2-ip-addressing)
- [Step 3: Switch ports (speed and duplex)](#step-3-switch-ports-speed-and-duplex)
- [Step 4: Secure remote access with SSH](#step-4-secure-remote-access-with-ssh)
- [Step 5: Verify, save, and troubleshoot](#step-5-verify-save-and-troubleshoot)
- [Background: Module 3 (Protocols and Models)](#background-module-3-protocols-and-models)
- [Command cheat sheet](#command-cheat-sheet)

## Overview

### What you need

- Packet Tracer, or a real Cisco switch with a console cable
- At least one PC connected to the switch (a PC icon in Packet Tracer)
- Your lab's addressing table (device names and IP addresses)

### Example addressing

Swap these for the values in your own lab.

| Device | Interface | IP address | Subnet mask | Default gateway |
|--------|-----------|------------|-------------|-----------------|
| S1 | VLAN 1 (SVI) | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| PC1 | NIC | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2 | NIC | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |

### CLI modes to know

| Prompt | Mode | How to get there |
|--------|------|------------------|
| `Switch>` | User EXEC | Default when you log in |
| `Switch#` | Privileged EXEC | `enable` |
| `Switch(config)#` | Global config | `configure terminal` |
| `Switch(config-if)#` | Interface config | `interface f0/1` |
| `Switch(config-line)#` | Line config | `line console 0` |

Use `exit` to go back one level and `end` (or Ctrl+Z) to jump straight back to Privileged EXEC. Pressing Tab finishes a command for you, and `?` shows what you can type next.

### Hot keys and shortcuts

The command line has shortcuts that save time and cut down on typos. These work on the switch and on routers.

**Typing commands**

| Shortcut | What it does |
|----------|--------------|
| Tab | Finishes a command you started. Type `conf` and press Tab to get `configure`. |
| `?` | Shows the commands you can use at that point. Type part of a command and then `?` to see what could finish it. |
| Up Arrow or Ctrl+P | Brings back the last command you typed. Keep pressing it to go further back. |
| Down Arrow or Ctrl+N | Goes forward again through the commands you already went back through. |
| Ctrl+R or Ctrl+I or Ctrl+L | Shows the system prompt and the command line again after a console message pops up while you are typing. |

**Moving the cursor**

| Shortcut | What it does |
|----------|--------------|
| Ctrl+A | Moves the cursor to the start of the line. |
| Ctrl+E | Moves the cursor to the end of the line. |
| Left Arrow or Ctrl+B | Moves the cursor back one character. |
| Right Arrow or Ctrl+F | Moves the cursor forward one character. |
| Esc B | Moves the cursor back one word. |
| Esc F | Moves the cursor forward one word. |

**Deleting text**

| Shortcut | What it does |
|----------|--------------|
| Backspace | Deletes the character to the left of the cursor. |
| Ctrl+D | Deletes the character the cursor is on. |
| Ctrl+K | Deletes everything from the cursor to the end of the line. |
| Ctrl+U or Ctrl+X | Deletes everything from the cursor back to the start of the line. |
| Ctrl+W | Deletes the word to the left of the cursor. |
| Esc D | Deletes from the cursor to the end of the word. |

**Getting out of things**

| Shortcut | What it does |
|----------|--------------|
| Ctrl+Z | Leaves any config mode and goes straight back to Privileged EXEC (`S1#`). |
| Ctrl+C | Also ends config mode and goes back to Privileged EXEC. It also cancels setup mode. |
| Ctrl+Shift+6 | The break key. It stops things that are stuck, like a ping, a traceroute, or a hostname lookup after a typo. |

### Shortening commands

You only have to type enough of a command for it to be the only match. Some common ones:

| Short version | Full command |
|---------------|--------------|
| `en` | `enable` |
| `conf t` | `configure terminal` |
| `int f0/1` | `interface fastethernet 0/1` |
| `sh run` | `show running-config` |
| `sh ip int br` | `show ip interface brief` |
| `copy run start` | `copy running-config startup-config` |


If you type too little and more than one command matches, the switch says the command is ambiguous. Type a few more letters and try again.

## Background: Module 1 (Networking Today)

This part is mostly ideas, not commands. It explains what the devices in your lab are and why networks are built the way they are.

### Hosts and devices

Every device on a network falls into one of two groups.

**End devices** are the ones people use directly, like PCs, laptops, phones, printers, and servers. Any end device that has an IP address is called a **host**. Hosts can act as:

- **Clients**, which ask for information (like your PC opening a web page).
- **Servers**, which run software that gives information to clients (like a web server or email server).
- **Peers**, which do both. In a peer-to-peer setup, one PC might share a file and also ask for files from another PC. This is easy to set up for a small home or office. The downsides are no central control, weaker security, and it gets slow and messy as it grows.

**Intermediary devices** connect the end devices together and move data between them. Examples are switches, routers, wireless access points, and firewalls. They do things like:

- Pass the signal along and send it again so it does not fade out
- Keep track of the paths that exist in the network
- Pick another path if a link breaks
- Allow or block data based on security settings

In this guide, S1 is the intermediary device and PC1 and PC2 are the end devices.

### Network media

Media is what carries the data between devices.

| Media | How it sends data |
|-------|-------------------|
| Copper cable (twisted pair) | Electrical signals through metal wires |
| Fiber-optic cable | Pulses of light through glass or plastic strands |
| Wireless | Radio waves through the air |

### Ports, interfaces, and diagrams

A **NIC** (network interface card) is the part inside a device that connects it to the network. A **physical port** is the plug on the device where a cable goes in, and an **interface** is the port as the software sees it (like `f0/1` on your switch).

There are two kinds of network diagrams:

- A **physical topology** shows where the devices actually are and how the cables run.
- A **logical topology** shows the names, interfaces, and IP addressing, and does not care where things sit in the building. The addressing table at the top of this guide goes with the logical one.

### Types of networks

| Type | What it means |
|------|---------------|
| LAN (local area network) | Covers a small area like one building or floor. One organization controls it, and it is fast. |
| WAN (wide area network) | Covers a big area and connects LANs together. It is usually run by service providers, and it is slower than a LAN. |
| WLAN | A LAN that uses wireless instead of cables. |
| Internet | A worldwide group of networks joined together. No single person owns it. |
| Intranet | A private network that only people inside one organization can use. |
| Extranet | A private network that lets certain outside people in, like a supplier or customer, in a secure way. |

### Ways to connect to the internet

Home and small office:

- Cable (comes through the same line as cable TV)
- DSL (comes through the phone line)
- Cellular (uses the phone network)
- Satellite (good for rural places, but slower)
- Dial-up (very slow, uses a phone line, and is mostly used where nothing else exists)

Business:

- Dedicated leased line (a private connection that is always on)
- Metro Ethernet
- Business DSL
- Satellite

### What makes a network reliable

A good network has four things:

1. **Fault tolerance:** it keeps working when something breaks. It does this by having more than one path for data, so if one link fails the data goes another way.
2. **Scalability:** it can grow (more users, more devices) without the quality dropping.
3. **Quality of Service (QoS):** it can treat some traffic as more important than other traffic. Voice and video calls get priority because they cannot wait, and a delayed email is no big deal.
4. **Security:** it protects the network and the data on it. Security has three goals: **confidentiality** (only the right people can read the data), **integrity** (the data is not changed), and **availability** (the data and network are there when people need them).

A **converged network** is one network that carries data, voice, and video all together, instead of using separate networks for each.

### Network trends

- **BYOD (bring your own device):** people use their own phones and laptops at work or school.
- **Online collaboration and video communication:** tools for working together and meeting from far away.
- **Cloud computing:** using servers and storage run by someone else over the internet. There are four types: **public** (anyone can use it, like a big provider), **private** (made for one organization), **hybrid** (a mix of public and private), and **custom** (made for a certain group, like healthcare).
- **Smart home technology:** appliances, lights, and security systems that connect to the network.
- **Powerline networking:** sending data through the electrical wiring in a building.
- **Wireless broadband:** getting internet from a wireless provider, which is useful in places that cable and DSL do not reach.

### Network security

Common threats:

| Threat | What it is |
|--------|------------|
| Virus | Harmful code that attaches to a file and needs someone to open it to spread. |
| Worm | Harmful code that copies itself across the network on its own. |
| Trojan horse | Harmful code that looks like a normal program. |
| Spyware | Secretly watches what you do and collects information. |
| Adware | Shows unwanted ads. |
| Zero-day attack | An attack on a weakness that nobody has fixed yet. |
| DoS / DDoS attack | Floods a device or network with traffic so real users cannot get in. DDoS means it comes from many devices at once. |
| Data interception and theft | Someone captures information moving across the network. |
| Identity theft | Someone steals personal information to pretend to be you. |

Common ways to defend:

- Antivirus and antispyware software on end devices
- Firewalls to filter traffic coming in and out
- Access control lists (ACLs) to allow or block certain traffic
- Intrusion prevention systems (IPS) to spot and stop attacks
- VPNs (virtual private networks) for a safe, encrypted connection over the internet

The passwords, banner, and SSH setup in Steps 1 and 4 are part of this same idea, which is keeping the device secure.

## Background: Module 2 (Basic Switch and End Device Configuration)

This is the module all the commands in this guide come from. This part covers the basics you need before the steps: how you get into a device and how the command line works.

### Ways to access a Cisco device

| Method | What it is | When to use it |
|--------|-----------|----------------|
| Console | A cable goes straight from your computer to the console port on the device. It is "out-of-band," meaning it does not use the network. | First setup of a new device, or when the network is down. |
| Aux | Another out-of-band port, used with a modem and a phone line. | Reaching a router far away when the network is not working. |
| SSH | A remote login over the network. Everything is encrypted. | The best choice for remote access. |
| Telnet | A remote login over the network, but everything is sent in plain text. | Avoid it, since anyone watching the network can read it. |

A new switch has no IP address, so the console is the only way to configure it the first time. To use the console you need a terminal program like PuTTY, Tera Term, or SecureCRT on your computer.

### How IOS is built

Cisco's operating system is called **IOS**. It has three parts: the **hardware** (the physical device), the **kernel** (talks to the hardware), and the **shell** (the part you type into, called the CLI, or command-line interface). We use the CLI in this guide.

### Command structure

Every command is made of parts:

- The **command** is the first word, like `ping` or `show`.
- A **keyword** is a word that tells the command what to look at, like `ip interface brief`.
- An **argument** is a value you fill in yourself, like an IP address or a name.

Example: in `ping 192.168.1.2`, `ping` is the command and `192.168.1.2` is the argument. In `show ip interface brief`, `show` is the command and `ip interface brief` is the keywords.

### Getting help while typing

- `?` by itself lists every command you can use in the mode you are in.
- `sh?` (no space) lists every command that starts with "sh".
- `show ?` (with a space) lists the keywords that can come after `show`.

### Error messages

| Message | What it means | Fix |
|---------|--------------|-----|
| `% Incomplete command.` | You left something out, like a keyword or an argument. | Press Up Arrow, add the missing part, and use `?` to see what it needs. |
| `% Invalid input detected at '^' marker.` | You typed it wrong. The `^` points to where the switch got confused. | Check for a typo at the spot the `^` points to. |
| `% Ambiguous command: "show s"` | You did not type enough letters, so more than one command matches. | Type more letters of the command. |

### Useful show commands

| Command | What it shows |
|---------|---------------|
| `show running-config` | The settings the switch is using right now |
| `show startup-config` | The saved settings |
| `show interfaces` | Detailed status and counters for each interface |
| `show ip interface brief` | A short list of interfaces, their IP addresses, and if they are up |
| `show version` | The IOS version, how long the switch has been on, and the hardware |

## Step 1: Basic switch configuration

Give the switch a name and passwords before anything else. Run these in order from the console.

```
Switch> enable
Switch# configure terminal
Switch(config)# hostname S1
S1(config)# no ip domain-lookup
S1(config)# enable secret {add password}
S1(config)# service password-encryption
S1(config)# security passwords min-length 10
```

- `hostname` sets the device name so you know which box you are on.
- `no ip domain-lookup` stops the switch from trying to look up a typo as a hostname, which freezes the CLI for a while.
- `enable secret` sets an encrypted password for Privileged EXEC. Use this one, not `enable password`.
- `service password-encryption` scrambles the other plain text passwords in the config.
- `security passwords min-length 10` makes the switch reject short passwords from now on.

### Naming and password rules

A good device name:

- Starts with a letter
- Has no spaces
- Ends with a letter or a number
- Only uses letters, numbers, and dashes
- Is under 64 characters

A good password:

- Is at least 10 characters (the switch here rejects anything shorter)
- Mixes uppercase, lowercase, numbers, and special characters
- Is different on each device
- Is not a common word, since those are easy to guess

The console, VTY, and SSH passwords in this guide are just examples. Use your own.

### Lock down the console and add a banner

```
S1(config)# line console 0
S1(config-line)# password Cons-Pass1
S1(config-line)# login
S1(config-line)# logging synchronous
S1(config-line)# exit
S1(config)# line vty 0 15
S1(config-line)# password Vty-Pass-1
S1(config-line)# login
S1(config-line)# exit
S1(config)# banner motd #Authorized access only!#
```

`logging synchronous` keeps system messages from cutting into the middle of what you are typing. The VTY lines are the virtual lines used for remote logins. The `#` is just a delimiter, and it can be any character that is not in your message.

Be careful with the banner wording. A message like "Welcome!" can make it look like anyone is invited in, so keep it a warning for people who are not allowed.

### Save it

```
S1# copy running-config startup-config 
You can also type the short version, `copy run start`, and it does the same thing.


```

The running config lives in RAM and disappears on reboot. The startup config is saved in NVRAM and loads when the switch starts, so save after every set of changes. Check what is saved with `show startup-config`.

| File | Where it is stored | What it does |
|------|--------------------|--------------|
| `running-config` | RAM | The settings the switch is using right now. It is lost when the switch loses power or restarts. |
| `startup-config` | NVRAM | The saved copy that loads when the switch boots. It stays even with no power. |

**Undoing a bad configuration.** If you saved something wrong and want to start over, erase the saved file and restart the switch:

```
S1# erase startup-config
S1# reload
```

If you made a mistake but have not saved yet, run `reload` and answer **no** when it asks to save. The switch comes back with the last saved config.

**Backing up to a text file.** In a terminal program like Tera Term, turn on logging and then run `show running-config`. The whole config gets saved into a text file you can keep as a backup.

## Step 2: IP addressing

### IP address basics

- **IPv4** addresses are 32 bits long and written as four numbers, like `192.168.1.10`. They come with a subnet mask, like `255.255.255.0`, which says which part of the address is the network.
- **IPv6** addresses are 128 bits long and written in hexadecimal, like `2001:db8:acad:1::10/64`. They were made because IPv4 was running out of addresses.

A device connects to the network through a **network interface card (NIC)**, which is Ethernet for wired or WLAN for wireless. Switch ports work at layer 2 and do not have their own IP addresses. Router interfaces do. The console port is not a network port. It is only for managing the device directly.

### Give the switch a management IP

A switch does not put an IP address on its ports. The address goes on a virtual interface called an SVI (switch virtual interface), usually VLAN 1.

```
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.1.2 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.1.1
```

The SVI is shut down until you run `no shutdown`, and it will not come up unless at least one port in VLAN 1 is connected. The **default gateway** lets you reach the switch from a different network. Without it, only devices on the same subnet can manage it.

The default gateway is the IP address of the network router that connects a local network to the outside world. It acts like an "exit" door for traffic going to other networks outside your local network.

### Set up the end devices

Each PC needs an IP address, a subnet mask, and a default gateway that match the same network as the switch.

- In Packet Tracer, click the PC, go to Desktop, then IP Configuration, and pick Static.
- On a Windows PC, go to Network settings, then the adapter's IPv4 properties.

Check it from the PC command prompt with `ipconfig`.

A PC can get its address two ways. **Static** means you type the IP address, subnet mask, and default gateway yourself, which is used for things like servers and printers that need to stay the same. **DHCP** means the PC gets its address automatically from a DHCP server, which is how most regular PCs work. On a Windows PC, pick "Use the following IP address" for static or "Obtain an IP address automatically" for DHCP. In Packet Tracer, pick Static or DHCP.

## Step 3: Switch ports (speed and duplex)

Ports negotiate speed and duplex on their own by default, which works fine most of the time. You can set them by hand if a link does not come up right.

```
S1(config)# interface f0/1
S1(config-if)# description Link to PC1
S1(config-if)# duplex full
S1(config-if)# speed 100
S1(config-if)# mdix auto
S1(config-if)# exit
```

This sets the port to full duplex at 100 Mbps. To put it back on automatic, use `duplex auto` and `speed auto`. A duplex mismatch (full on one end, half on the other) causes a slow link with lots of errors, so keep both ends the same. `mdix auto` lets the port work with either a straight-through or a crossover cable.

## Step 4: Secure remote access with SSH

Telnet sends everything in plain text, so use SSH instead. The switch needs a hostname, a domain name, and an IP address first, which you set in Steps 1 and 2.

```
S1(config)# ip domain-name netacad.lab
S1(config)# username admin secret Adm1n-Pass
S1(config)# crypto key generate rsa modulus 1024
S1(config)# ip ssh version 2
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# exit
S1(config)# end
```

- `ip domain-name` gives the switch a domain name. SSH needs this to build its keys.
- `username admin secret` makes a login account. The password is stored encrypted.
- `crypto key generate rsa modulus 1024` makes the encryption keys SSH uses. A bigger number is stronger. SSH version 2 needs at least 768.
- `ip ssh version 2` makes the switch use the newer, safer version of SSH.
- `transport input ssh` lets only SSH into the VTY lines, so Telnet is blocked.
- `login local` makes the VTY lines check the username and password you just made, instead of the plain line password from Step 1.

### Check and use SSH

```
S1# show ip ssh
```

This shows that SSH is on and which version it uses.

To connect from a PC:

- In Packet Tracer, open the PC's Command Prompt and type `ssh -l admin 192.168.1.2`
- On a Windows PC, open Command Prompt and type `ssh admin@192.168.1.2`

Enter the password when it asks. If you get the `S1>` prompt, SSH works. Type `enable` and the enable secret to get to Privileged EXEC.

## Step 5: Verify, save, and troubleshoot

### Check the switch

```
S1# show ip interface brief
S1# show running-config
S1# show interfaces f0/1
S1# show ip ssh
```

- On `show ip interface brief`, the `Vlan1` line should show `192.168.1.2` and say `up` in both status columns.
- On `show interfaces f0/1`, check the speed and duplex match what you set in Step 3.

### Test connectivity

Run `ipconfig /all` on the PC first. It shows the IP address, subnet mask, default gateway, and also the MAC address and DHCP info.

Then ping in order, from closest to farthest. If one step fails, you know where the problem is.

1. `ping 127.0.0.1` tests the PC's own network software. If this fails, the problem is on the PC.
2. `ping 192.168.1.1` tests the connection to the default gateway.
3. `ping 192.168.1.2` tests the connection to the switch.
4. `ping 192.168.1.11` tests the connection to PC2.

If your lab has no router at 192.168.1.1, the gateway ping will time out, and that is expected.

On a PC, a good ping shows "Reply from..." and a bad one shows "Request timed out." On the switch, ping shows symbols:

| Symbol | Meaning |
|--------|---------|
| `!` | The ping got a reply (worked) |
| `.` | The ping timed out (failed) |

`!!!!!` with "Success rate is 100 percent" means everything works.

### Save your work

```
S1# copy running-config startup-config
```

Do this when everything works. If you skip it, all of your changes are gone the next time the switch restarts.

### Troubleshooting

| Problem | Likely cause | Fix |
|---------|-------------|-----|
| `Vlan1` says "administratively down" | You forgot `no shutdown` | `interface vlan 1`, then `no shutdown` |
| `Vlan1` is down | No port in VLAN 1 is connected, or the cable is bad | Plug a device into a port and check the cable |
| PC cannot ping the switch | Wrong IP address or mask, or the PC and switch are on different networks | Compare `ipconfig` on the PC with the addressing table |
| Ping to the gateway times out | The gateway address is wrong, or there is no router in the lab | Check the gateway address on the PC and the switch |
| Link is slow and has lots of errors | Duplex mismatch | Set both ends the same, or put both on `duplex auto` |
| SSH will not connect | Missing domain name, RSA keys, `transport input ssh`, or `login local` | Go back through Step 4 and check each line with `show running-config` |
| Settings gone after a restart | You did not save | `copy running-config startup-config` |
| CLI freezes after a typo | The switch is trying to look up the typo as a hostname | Press Ctrl+Shift+6 to stop it, and use `no ip domain-lookup` |
| `% Incomplete command.` or `% Invalid input` | A missing word or a typo | Use Up Arrow, fix it, and use `?` to check |

## Background: Module 3 (Protocols and Models)

Modules 1 and 3 are the "why" behind the commands. This part explains the rules devices follow to talk to each other.

### What is a protocol?

A **protocol** is a set of rules for communicating. Two devices can only talk if they follow the same rules. A protocol sets things like:

- How a message is formatted and wrapped
- How big each piece of the message can be
- When to send and how fast (timing)
- How to deal with a reply that never comes
- Whether the message goes to one device (**unicast**), a group (**multicast**), or everyone (**broadcast**)

A group of protocols that work together is called a **protocol suite**. The one the internet uses is **TCP/IP**.

### Common protocols

| Layer | Protocol | What it does |
|-------|----------|--------------|
| Application | HTTP / HTTPS | Loads web pages (HTTPS is the encrypted version) |
| Application | DNS | Turns a name like `example.com` into an IP address |
| Application | DHCP | Gives devices an IP address automatically |
| Application | SMTP, POP3, IMAP | Send and receive email |
| Application | FTP, TFTP | Move files between devices |
| Application | SSH, Telnet | Remote login |
| Transport | TCP | Reliable delivery. It sets up a connection, checks that everything arrived, and puts it in order. |
| Transport | UDP | Fast delivery with no checking. Good for video calls and games, where speed matters more than a few lost pieces. |
| Internet | IPv4, IPv6 | Addresses devices and picks the path for packets |
| Internet | ICMP | Sends error messages and tests (this is what `ping` uses) |
| Internet | OSPF, EIGRP, BGP | Routing protocols that routers use to share paths |
| Network Access | Ethernet | Rules for wired LANs |
| Network Access | WLAN (Wi-Fi) | Rules for wireless LANs |
| Network Access | ARP | Finds the MAC address that goes with an IP address |

### Standards organizations

Standards are open rules so that devices from different companies can work together.

- **IETF** (Internet Engineering Task Force) writes the internet standards
- **ICANN** and **IANA** handle domain names and IP addresses
- **IEEE** (Institute of Electrical and Electronics Engineers) makes standards for Ethernet and Wi-Fi
- **ISO** (International Organization for Standardization) made the OSI model
- **EIA / TIA** make standards for cabling
- **ITU-T** makes standards for telecom

### Why we use layered models

A **model** is a way to organize and explain how networking works. A layered model splits the job of networking into layers, and each layer has its own job. This helps in a few ways:

- It helps people design protocols, because each protocol works at one layer and knows what the layers above and below it do.
- It lets different companies make products that work together, as long as they follow the same layers.
- A change in one layer does not break the others. For example, a new type of cable does not mean the web browser has to be rewritten.
- It gives everyone the same words to talk about a problem, like "that is a layer 1 problem."

There are two main models, the **OSI model** and the **TCP/IP model**. The OSI model is a **reference model**, which means it explains what each layer should do but not exactly how. The TCP/IP model is a **protocol model**, which means it matches how the TCP/IP protocols are really built and used on the internet.

### The OSI model (7 layers)

| Layer | Name | What it does | Examples | Data is called |
|-------|------|--------------|----------|----------------|
| 7 | Application | Has the protocols that let programs talk to each other. | HTTP, DNS, SMTP | Data |
| 6 | Presentation | Puts the data in a common format so both sides understand it. It also handles compression and encryption. | Image and text formats, encryption | Data |
| 5 | Session | Starts, manages, and ends the conversation between two devices. | Login sessions | Data |
| 4 | Transport | Breaks data into pieces, sends them, and puts them back together for each conversation between end devices. | TCP, UDP | Segment |
| 3 | Network | Moves the pieces between devices across different networks using IP addresses. | IP, ICMP | Packet |
| 2 | Data Link | Moves frames between devices on the same link using MAC addresses. | Ethernet, Wi-Fi | Frame |
| 1 | Physical | Sends the actual bits as electrical signals, light, or radio waves. | Cables, ports, wireless signals | Bits |

Two tricks for remembering the layers:

- Top to bottom (7 to 1): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing
- Bottom to top (1 to 7): **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

### The TCP/IP model (4 layers)

| Layer | What it does | Protocol examples |
|-------|--------------|-------------------|
| Application | Gives the data to the user and handles encoding and the back-and-forth of the conversation. | HTTP, HTTPS, DNS, DHCP, SMTP, FTP |
| Transport | Handles communication between devices across different networks. | TCP, UDP |
| Internet | Finds the best path through the network. | IPv4, IPv6, ICMP |
| Network Access | Controls the hardware and media that make up the network. | Ethernet, Wi-Fi, ARP |

### OSI vs TCP/IP side by side

| OSI model (7 layers) | TCP/IP model (4 layers) |
|----------------------|-------------------------|
| 7. Application | Application |
| 6. Presentation | Application |
| 5. Session | Application |
| 4. Transport | Transport |
| 3. Network | Internet |
| 2. Data Link | Network Access |
| 1. Physical | Network Access |

**What they have in common:**

- Both are built in layers.
- Both have an application layer, even though what it covers is different.
- Both have comparable transport and network (internet) layers.
- Both assume data is sent in packets that can take different paths.
- A person who works with networks needs to know both.

**How they are different:**

- TCP/IP joins the OSI Application, Presentation, and Session layers into one Application layer.
- TCP/IP joins the OSI Data Link and Physical layers into one Network Access layer.
- TCP/IP has fewer layers, so it is simpler.
- The internet was built on the TCP/IP protocols, so that model is the one with real-world use. The OSI model is mostly used as a guide to explain things and to troubleshoot.

### Which layer do devices work at?

| Device or part | Layer |
|----------------|-------|
| Cables, ports, and hubs | 1 (Physical) |
| NIC and switch | 2 (Data Link) |
| Router | 3 (Network) |

A switch moves frames using MAC addresses, which is layer 2. In this guide we also gave S1 an IP address, but that is only so you can manage it remotely. It does not make the switch route traffic between networks.

### How data moves down the layers (web page example)

Say PC1 opens a web page. On the way out, the data goes down through the TCP/IP layers:

1. **Application:** the web browser makes an HTTP request.
2. **Transport:** TCP breaks the data into segments. It adds sequence numbers and port numbers. The destination port is 80 for HTTP or 443 for HTTPS, and it tells the server which program should get the data.
3. **Internet:** IP puts each segment in a packet and adds the source and destination IP addresses.
4. **Network Access:** Ethernet puts the packet in a frame and adds the source and destination MAC addresses. Then the frame is sent out as bits.

When the server gets the bits, it goes back up the layers in reverse order. Each layer takes off its own header until the web server gets the request. The Data encapsulation section below goes into this process in more detail.

### Using the models to troubleshoot

The layers give you a way to check a problem in order, usually from the bottom up. Here are examples from the lab in this guide:

| Layer | Example problem | How you would check |
|-------|-----------------|---------------------|
| 1 (Physical) | Cable unplugged or bad, port is down | Look at the cable and `show ip interface brief` |
| 2 (Data Link) | Duplex mismatch between the switch and the PC | `show interfaces f0/1` |
| 3 (Network) | Wrong IP address, mask, or default gateway | `ipconfig` on the PC and `ping` |
| 4 to 7 (Transport to Application) | SSH is not set up right, or the wrong port or app settings | `show ip ssh` and `show running-config` |

If the cable is unplugged, nothing above it will work. That is why you check layer 1 first.

### Data encapsulation

Before data is sent, it gets split into smaller pieces. This is called **segmentation**. Smaller pieces are faster, and they let many conversations share the same link at the same time. Each piece is numbered, which is called **sequencing**, so the other side can put them back in the right order.

As each piece goes down the layers, each layer adds its own information (a header) around it. This is called **encapsulation**. The name for the data changes at each layer:

| Layer | Name of the data (PDU) |
|-------|------------------------|
| Application | Data |
| Transport | Segment |
| Internet / Network | Packet |
| Network Access / Data Link | Frame |
| Physical | Bits |

When the data gets to the other device, it goes back up the layers and each layer takes off its own header. This is called **de-encapsulation**.

### IP addresses vs MAC addresses

Every message has two kinds of addresses, and they do different jobs.

| Address | Layer | What it does | Does it change along the way? |
|---------|-------|--------------|-------------------------------|
| IP address (source and destination) | Network (3) | Says where the data started and where it is finally going | No, it stays the same the whole trip |
| MAC address (source and destination) | Data Link (2) | Gets the data across one link, to the next device | Yes, it changes at every router |

A MAC address is 48 bits long and is written as 12 hex digits, like `00-1A-2B-3C-4D-5E`. It is burned into the NIC.

Two cases using the addressing table from the Overview:

- **Same network:** PC1 (192.168.1.10) pings S1 (192.168.1.2). They are on the same network, so PC1 sends the frame straight to S1's MAC address.
- **Different network:** if PC1 pings something outside 192.168.1.0/24, PC1 sends the frame to the **default gateway's** MAC address (192.168.1.1). The router then sends it on. The destination IP address stays the same, but the MAC addresses change at each step.

This is why every device needs a default gateway set. Without it, a device cannot send anything outside its own network.

**ARP** (Address Resolution Protocol) is how a device finds a MAC address when it only knows the IP address. It asks the whole network "who has this IP?" and the right device answers with its MAC address.

### Key points to remember

- The OSI model has 7 layers and the TCP/IP model has 4.
- OSI is a reference model for explaining things. TCP/IP is the model the internet actually uses.
- The data has a different name at each layer: data, segment, packet, frame, and bits.
- Layer 3 uses IP addresses and layer 2 uses MAC addresses.
- Ping uses ICMP, which works at the Internet (network) layer.

## Command cheat sheet

| Command | What it does |
|---------|--------------|
| `enable` | Goes from User EXEC to Privileged EXEC |
| `configure terminal` | Goes into global config mode |
| `exit` | Goes back one level |
| `end` or Ctrl+Z | Jumps back to Privileged EXEC from any config mode |
| `?` | Lists the commands or keywords you can use at that point |
| `hostname name` | Names the device |
| `no ip domain-lookup` | Stops the switch from looking up typos as hostnames |
| `enable secret password` | Sets the encrypted Privileged EXEC password |
| `service password-encryption` | Scrambles plain text passwords in the config |
| `security passwords min-length 10` | Rejects passwords shorter than 10 characters |
| `line console 0` | Goes into console line config |
| `line vty 0 15` | Goes into the remote (VTY) line config |
| `password password` | Sets the password on a line |
| `login` | Makes a line ask for its password |
| `logging synchronous` | Keeps system messages from cutting into what you type |
| `banner motd #message#` | Sets the login warning message |
| `interface vlan 1` | Goes into the switch virtual interface (SVI) |
| `ip address address mask` | Sets an IPv4 address |
| `no shutdown` | Turns an interface on |
| `ip default-gateway address` | Sets the switch's default gateway |
| `interface f0/1` | Goes into a port's interface config |
| `description text` | Adds a label to an interface |
| `duplex full` / `duplex auto` | Sets the duplex on a port |
| `speed 100` / `speed auto` | Sets the speed on a port |
| `mdix auto` | Lets a port work with straight-through or crossover cables |
| `ip domain-name name` | Sets the domain name (needed for SSH) |
| `username name secret password` | Makes a login account |
| `crypto key generate rsa modulus 1024` | Makes the SSH encryption keys |
| `ip ssh version 2` | Makes the switch use SSH version 2 |
| `transport input ssh` | Lets only SSH into the VTY lines |
| `login local` | Makes the line use the username and password list |
| `show running-config` | Shows the settings in use right now |
| `show startup-config` | Shows the saved settings |
| `show interfaces` | Shows detailed info for each interface |
| `show ip interface brief` | Shows a short list of interfaces and their IP addresses |
| `show ip ssh` | Shows if SSH is on and which version |
| `show version` | Shows the IOS version and hardware info |
| `copy running-config startup-config` (or `copy run start`) | Saves the configuration |
| `erase startup-config` | Deletes the saved configuration |
| `reload` | Restarts the switch |
| `ping address` | Tests connectivity |
| `ipconfig` / `ipconfig /all` | Shows the IP settings on a Windows PC |