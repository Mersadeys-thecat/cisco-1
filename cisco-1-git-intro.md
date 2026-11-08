# My First Git Lab

This is my first document stored in Git for Cisco one. 

# Cisco Networking Setup Guide

Setup notes for **CCNA: Introduction to Networks** 

Modules 1 (Networking Today) and 3 (Protocols and Models) are mostly ideas like network types, TCP/IP, and the OSI model. The commands all come from module 2 (Basic Switch and End Device Configuration). Follow the steps in order and you end up with a switch that is named, locked down, addressed, reachable over SSH, and saved.

## Table of Contents

- [Overview](#overview)
- [Step 1: Basic switch configuration](#step-1-basic-switch-configuration)
- [Step 2: IP addressing](#step-2-ip-addressing)
- [Step 3: Switch ports (speed and duplex)](#step-3-switch-ports-speed-and-duplex)
- [Step 4: Secure remote access with SSH](#step-4-secure-remote-access-with-ssh)
- [Step 5: Verify, save, and troubleshoot](#step-5-verify-save-and-troubleshoot)
- [Command cheat sheet](#command-cheat-sheet)

---

## Overview

### What you need

- Packet Tracer, or a real Cisco switch with a console cable
- At least one PC connected to the switch (a PC icon in Packet Tracer)
- Your lab's addressing table (device names and IP addresses)

### Example addressing

Swap these for the values in your own lab.

| Device | Interface | IP address | Subnet mask | Default gateway |
| --- | --- | --- | --- | --- |
| S1 | VLAN 1 (SVI) | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| PC1 | NIC | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2 | NIC | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |

### CLI modes to know

| Prompt | Mode | How to get there |
| --- | --- | --- |
| `Switch>` | User EXEC | Default when you log in |
| `Switch#` | Privileged EXEC | `enable` |
| `Switch(config)#` | Global config | `configure terminal` |
| `Switch(config-if)#` | Interface config | `interface f0/1` |
| `Switch(config-line)#` | Line config | `line console 0` |

Use `exit` to go back one level and `end` (or Ctrl+Z) to jump straight back to Privileged EXEC. Pressing Tab finishes a command for you, and `?` shows what you can type next.

---

## Step 1: Basic switch configuration

Give the switch a name and passwords before anything else. Run these in order from the console.

```text
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

### Lock down the console and add a banner

```text
S1(config)# line console 0
S1(config-line)# password Cons-Pass1
S1(config-line)# login
S1(config-line)# logging synchronous
S1(config-line)# exit
S1(config)# line vty 0 15
S1(config-line)# password Vty-Pass1
S1(config-line)# login
S1(config-line)# exit
S1(config)# banner motd #Authorized access only!#
```

`logging synchronous` keeps system messages from cutting into the middle of what you are typing. The VTY lines are the virtual lines used for remote logins. The `#` is just a delimiter, and it can be any character that is not in your message.

### Save it

```text
S1# copy running-config startup-config
```

The running config lives in RAM and disappears on reboot. The startup config is saved in NVRAM and loads when the switch starts, so save after every set of changes. Check what is saved with `show startup-config`.

---

## Step 2: IP addressing

### Give the switch a management IP

A switch does not put an IP address on its ports. The address goes on a virtual interface called an SVI (switch virtual interface), usually VLAN 1.

```text
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.1.2 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.1.1
```

The SVI is shut down until you run `no shutdown`, and it will not come up unless at least one port in VLAN 1 is connected. The **default gateway** lets you reach the switch from a different network. Without it, only devices on the same subnet can manage it.

**Deafault gateway** is the IP address of the netwrork router that connects a local network to the outside wrold. Acts as an 'exit' door traveling to other destinations outside your local network. 

### Set up the end devices

Each PC needs an IP address, a subnet mask, and a default gateway that match the same network as the switch.

- In Packet Tracer, click the PC, go to Desktop, then IP Configuration, and pick Static.
- On a Windows PC, go to Network settings, then the adapter's IPv4 properties.

Check it from the PC command prompt with `ipconfig`.

---

## Step 3: Switch ports (speed and duplex)

Ports negotiate speed and duplex on their own by default, which works fine most of the time. You can set them by hand if a link does not come up right.

```text
S1(config)# interface f0/1
S1(config-if)# description Link to PC1
S1(config-if)# duplex full
S1(config-if)# speed 100
S1(config-if)# mdix auto
S1(config-if)# exit
```

This sets the port to full duplex at 100 Mbps. To put it back on automatic, use `duplex auto` and `speed auto`. A **duplex mismatch** (full on one end, half on the other) causes a slow link with lots of errors, so keep both ends the same. `mdix auto` lets the port work with either a straight-through or a crossover cable.

---

## Step 4: Secure remote access with SSH

Telnet sends everything in plain text, so use SSH instead. The switch needs a hostname, a domain name, and an IP address first, which you set in Steps 1 and 2.

```text
S1(config)# ip domain-name netacad.lab
S1(config)# username admin secret Adm1n-Pass
S1(c

