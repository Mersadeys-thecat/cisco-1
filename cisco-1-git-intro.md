# My First Git Lab

This is my first document stored in Git for Cisco one.

# Cisco Networking Setup Guide

Setup notes for CCNA: Introduction to Networks

Modules 1 (Networking Today) and 3 (Protocols and Models) are mostly ideas like network types, TCP/IP, and the OSI model, and they are explained in the Background sections of this guide. The commands all come from module 2 (Basic Switch and End Device Configuration). Everything is written for Cisco Packet Tracer, so each step has its own Packet Tracer notes, and there is a full walkthrough of the Packet Tracer activity near the end. Follow the steps in order and you end up with a switch that is named, locked down, addressed, reachable over SSH, and saved.

## Table of Contents

- [Overview](#overview)
- [Packet Tracer Basics](#packet-tracer-basics)
- [Dummy's Guide: Set Up a Switch in Packet Tracer](#dummys-guide-set-up-a-switch-in-packet-tracer)
- [Background: Module 1 (Networking Today)](#background-module-1-networking-today)
- [Background: Module 2 (Basic Switch and End Device Configuration)](#background-module-2-basic-switch-and-end-device-configuration)
- [Step 1: Basic switch configuration](#step-1-basic-switch-configuration)
- [Step 2: IP addressing](#step-2-ip-addressing)
- [Step 3: Switch ports (speed and duplex)](#step-3-switch-ports-speed-and-duplex)
- [Step 4: Secure remote access with SSH](#step-4-secure-remote-access-with-ssh)
- [Step 5: Verify, save, and troubleshoot](#step-5-verify-save-and-troubleshoot)
- [Packet Tracer Lab: Activity 2.9.1](#packet-tracer-lab-activity-291)
- [Background: Module 3 (Protocols and Models)](#background-module-3-protocols-and-models)
- [Command cheat sheet](#command-cheat-sheet)
- [Packet Tracer cheat sheet](#packet-tracer-cheat-sheet)

## Overview

### What you need

- Cisco Packet Tracer (free from Cisco Networking Academy), or a real Cisco switch with a console cable
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

## Packet Tracer Basics

This guide is meant to be done in Cisco Packet Tracer, so this part shows you around before you start typing commands. It works the same for the example lab in this guide and for the Packet Tracer activities from class.

### The Packet Tracer screen

| Part | Where it is | What it does |
|------|-------------|--------------|
| Workspace | The big middle area | Where your devices and cables go. There are two views. **Logical** shows devices and cables like a diagram, and **Physical** shows rooms and racks. Use Logical for everything in this guide. |
| Device-type box | Bottom left | Lets you pick a kind of device: Network Devices (routers and switches), End Devices (PCs), Components, Connections (cables), Miscellaneous, and Multiuser. |
| Device list | Next to the device-type box | Shows the actual devices of that kind, like a 2960 switch or a PC. |
| Realtime / Simulation | Bottom right of the workspace | **Realtime** is normal. **Simulation** lets you watch packets move one step at a time. |
| Time and fast-forward | Under the workspace, next to the Time clock | The fast-forward button skips ahead so link lights turn green faster. |
| Top toolbar | Top of the window | Save, open, zoom, and tools like Select, Delete (the X), Inspect, and Add Simple PDU (the closed envelope). |
| PT Activity window | A separate window, only for activity files | Has the instructions, the addressing table, and the **Check Results** button. |

### Tabs when you click a device

| Device | Tabs | What they do |
|--------|------|--------------|
| Switch | Physical, Config, CLI, Attributes | Physical shows the box and the power switch. Config has simple menus. **CLI** is where you type commands. |
| PC | Physical, Config, Desktop, Programming, Attributes | **Desktop** has the tools you use the most: IP Configuration, Command Prompt, Terminal, and Telnet / SSH Client. |

### Cables

Click the **Connections** icon (the lightning bolt) in the device-type box to see the cables.

| Cable | What it looks like | Use it for |
|-------|--------------------|-----------|
| Console | Light blue wavy line | PC `RS 232` port to switch `Console` port. This is the one for configuring a switch the first time. |
| Copper straight-through | Solid black line | PC to switch, switch to router |
| Copper cross-over | Dashed black line | Switch to switch, PC to PC |
| Automatically Choose (lightning bolt) | Packet Tracer picks for you | Fine for normal labs, but do not use it when a lab says to use a console cable |

To connect two devices: pick the cable, click the first device and choose a port from the menu, then click the second device and choose its port. To remove a cable, use the Delete tool (the X in the toolbar) and click it.

Newer switches can tell which cable type is plugged in (this is what `mdix auto` does), so a straight-through cable between switches may still work in Packet Tracer. The textbook answer is still cross-over for switch to switch, so use that when a lab asks.

### Link lights

The little triangle at the end of each cable shows the port status.

| Light | What it means |
|-------|---------------|
| Green | The link is up and working |
| Amber (orange) | The switch port is still starting up. Wait about 30 seconds, or click the fast-forward button. |
| Red | The link is down. Check that the cable is on the right port, that both devices are on, and that the port is not shut down. |
| No light at all on a console cable | Normal. Console cables do not show link lights. |

### Build the example lab from scratch

This builds the lab used in the rest of this guide (S1, PC1, and PC2).

1. In the device-type box, click **Network Devices**, then **Switches**, and drag a **2960** switch onto the workspace.
2. Click **End Devices** and drag two **PC**s onto the workspace.
3. Click the **Connections** icon and pick **Copper Straight-Through**. Click PC1, choose `FastEthernet0`, then click the switch and choose `FastEthernet0/1`.
4. Do the same for PC2, and plug it into `FastEthernet0/2` on the switch.
5. Pick the **Console** cable. Click PC1, choose `RS 232`, then click the switch and choose `Console`.
6. Wait for the green lights, or click the fast-forward button.

To rename a device label, click the device, go to the **Config** tab, and change the **Display Name**. The display name only changes the label on the screen. It does not change the switch name you see in the CLI, because that comes from the `hostname` command.

### Console connection: Terminal vs the CLI tab

There are two places to type switch commands, and they are not the same thing.

| Way | How to open it | When to use it |
|-----|----------------|----------------|
| **Terminal** (a real console connection) | Click the PC that has the console cable, go to **Desktop**, then **Terminal**, and click **OK** | When a lab says to use a console connection. This needs the console cable between the PC and the switch. |
| **CLI tab** | Click the switch, then the **CLI** tab | Quick config when the lab does not mention a console. It does not need a cable. |

The Terminal window has settings (9600 bits per second, 8 data bits, no parity, 1 stop bit, no flow control). Leave them at the defaults and click OK. Press Enter until you see a prompt. If the window stays blank, check that the console cable goes from `RS 232` on the PC to `Console` on the switch.

### Tools on a PC's Desktop tab

| Tool | What it does |
|------|--------------|
| IP Configuration | Sets the IP address, subnet mask, and default gateway (static), or turns on DHCP |
| Command Prompt | Lets you run `ipconfig`, `ipconfig /all`, `ping`, and `ssh` |
| Terminal | The console connection to a switch (needs the console cable) |
| Telnet / SSH Client | Another way to log into a switch over the network |

**Subnet mask warning:** when you type an IP address in IP Configuration, Packet Tracer fills in the subnet mask by itself, and it can be wrong. For `192.168.1.x` it picks `255.255.255.0`, which is right for this guide. For `128.107.20.x` it picks `255.255.0.0`, which is wrong for a lab that uses `255.255.255.0`. Always look at the Subnet Mask box after you type the IP address and fix it if it is wrong.

### Simulation mode (see Module 3 in action)

Simulation mode shows packets moving through your network, which is a good way to see the models from Module 3.

1. Click **Simulation** (bottom right of the workspace).
2. Click the **Add Simple PDU** tool (the closed envelope in the toolbar), then click the source device and the destination device.
3. Click the **Capture / Forward** button to move the packet one step.
4. Click the little envelope on the screen to open it. The **OSI Model** tab shows what happens at each layer, and the **Inbound PDU Details** and **Outbound PDU Details** tabs show the headers and addresses.

Click **Realtime** to go back to normal when you are done.

### Saving in Packet Tracer

There are two different things to save, and you need both.

1. **The switch config:** type `copy running-config startup-config` (or `copy run start`) on each switch. If you skip this, the config is gone when the switch restarts.
2. **The Packet Tracer file:** press Ctrl+S or use File, then Save. This saves your devices, cables, and the saved switch configs. If you reopen the file later, only what you saved with `copy run start` is still on the switches.

## Dummy's Guide: Set Up a Switch in Packet Tracer

This is the exact order to set up a switch, every time. Do the steps in this order, type exactly what is shown, and check what you should see before you move on to the next one. Anything in {curly braces} is a spot where you type your own value. Don't type the curly braces.

### Before you start

- [ ] The switch and PC are on the workspace and connected with a straight-through cable
- [ ] The console cable goes from the PC's `RS 232` port to the switch's `Console` port
- [ ] The link lights are green (click the fast-forward button if they are amber)
- [ ] You filled in the sheet below

**Fill-in sheet** (write your values here before you start typing):

| What you need | Your value |
|---------------|------------|
| Switch name (hostname) | {name} |
| Enable secret (the Privileged EXEC password) | {secret} |
| Line password (for the console and VTY lines) | {password} |
| Banner message | {message} |
| Switch IP address | {ip} |
| Subnet mask | {mask} |
| Default gateway (only if your lab gives one) | {gateway} |

### Step A: Open the console

1. Click the PC, then **Desktop**, then **Terminal**, then **OK**.
2. Press Enter.

**You should see:** `Switch>`

If you see `User Access Verification` and a `Password:` prompt, the switch already has a password, so type it. If the window is blank, check the console cable and press Enter again.

### Step B: Get into configuration mode

```
enable
configure terminal
```

**You should see:** `Switch(config)#`

The prompt changes twice: `Switch>` becomes `Switch#` after `enable`, and `Switch#` becomes `Switch(config)#` after `configure terminal`. Don't go on until you see `(config)#`.

### Step C: Name the switch

```
hostname {name}
```

**You should see:** the prompt changes to your name. For example, `hostname ASw-1` makes the prompt `ASw-1(config)#`.

The name can't have spaces, it should start with a letter, and capital letters matter.

### Step D: Add the enable secret (the secret version of a password)

```
enable secret {secret}
```

For example: `enable secret C4aJa`

- Type `enable secret`, a space, and then the password. Nothing else.
- **You should see:** nothing. The prompt stays the same and no message shows up. That is normal.
- Use `secret` and not `password`. A secret is stored scrambled in the config. The older `enable password` command is stored as plain text, so don't use it.
- Passwords are case-sensitive, so type the capitals exactly.
- From now on, typing `enable` will ask for `Password:`. This is the password it wants.

**Which passwords can use `secret`?**

| Password for | Command | Can it use `secret`? |
|--------------|---------|----------------------|
| Privileged EXEC (the `enable` password) | `enable secret {secret}` | Yes |
| Console line | `password {password}` under `line console 0` | No, a line only has `password` |
| VTY lines (remote logins) | `password {password}` under `line vty 0 15` | No, a line only has `password` |
| A login username (for SSH) | `username {user} secret {password}` | Yes |

### Step E: Add the console password

```
line console 0
password {password}
login
exit
```

**You should see:** the prompt goes to `ASw-1(config-line)#` after the first line, and back to `ASw-1(config)#` after `exit`.

`login` is what makes the switch actually ask for the password. If you leave it out, the password is set but never checked.

### Step F: Add the VTY (remote login) password

```
line vty 0 15
password {password}
login
exit
```

**You should see:** the same prompt changes as in Step E. The `0 15` means all 16 remote login lines.

### Step G: Scramble the line passwords

```
service password-encryption
```

**You should see:** nothing. This hides the console and VTY passwords in the config so someone can't read them over your shoulder.

### Step H: Add the MOTD banner

```
banner motd #{message}#
```

For example: `banner motd #Authorized access only!#`

- The `#` marks where the message starts and ends. Use it on both sides, with no spaces.
- Pick a character that is not in your message. If your message has a `#` in it, use `$` or `%` instead.
- Make the message a warning. Don't write "Welcome," since that makes it sound like anyone can come in.

### Step I: Give the switch an IP address

```
interface vlan 1
ip address {ip} {mask}
no shutdown
exit
```

For example: `ip address 128.107.20.10 255.255.255.0`

**You should see:** the prompt goes to `ASw-1(config-if)#`. After `no shutdown`, a message like `%LINK-5-CHANGED: Interface Vlan1, changed state to up` may pop up. That is good.

Type the IP address and mask exactly. A typo here is an easy way to lose points.

### Step J: Set the default gateway (only if your lab gives one)

```
ip default-gateway {gateway}
```

If your addressing table has no gateway (like Activity 2.9.1), skip this step.

### Step K: Go back to Privileged EXEC

```
end
```

**You should see:** `ASw-1#` with a `#` and no `(config)` in it.

### Step L: Save

```
copy running-config startup-config
```

**You should see:** `Destination filename [startup-config]?` Press Enter. Then it shows `Building configuration...` and `[OK]`.

The short version is `copy run start`. If you skip this step, everything is lost when the switch restarts.

### Step M: Check your work

```
show running-config
show ip interface brief
```

**What to look for** in `show running-config` (press the Space bar when it shows `--More--` to see the next page, or `q` to stop):

```
hostname ASw-1
!
enable secret 5 $1$mERr$...
!
service password-encryption
!
interface Vlan1
 ip address 128.107.20.10 255.255.255.0
!
banner motd ^CAuthorized access only!^C
!
line con 0
 password 7 08...
 login
!
line vty 0 4
 password 7 08...
 login
line vty 5 15
 password 7 08...
 login
```

Your scrambled text will look different from this, and that's fine. Check that the hostname, banner, IP address, and the `password` and `login` lines are all there. The `^C` on the banner line is how the switch shows the `#` marks.

In `show ip interface brief`, the `Vlan1` line should show your IP address and say `up` and `up` (wait for the green lights if it doesn't).

### Step N: Test your login

1. Type `exit` at the `ASw-1#` prompt.
2. Press Enter.

**You should see:** your banner message, then `User Access Verification`, then `Password:`.

3. Type the **line password** (nothing shows as you type). You should get `ASw-1>`.
4. Type `enable`. It asks for `Password:`. Type the **enable secret**. You should get `ASw-1#`.

If both passwords work, you are done.

### Optional: use secret for the lines too

The console and VTY lines only have `password`. If you want them to use a secret anyway, make a username with a secret and tell the lines to use it. Only do this when your lab does not say to use one password for all lines.

```
username {user} secret {password}
line console 0
login local
exit
line vty 0 15
login local
exit
```

After this, the console and remote logins ask for a username and a password, and the password is stored as a secret.

### Copy-and-paste template

Replace each {value} before you type it. If you skip the gateway, delete that line.

```
enable
configure terminal
hostname {name}
enable secret {secret}
line console 0
password {password}
login
exit
line vty 0 15
password {password}
login
exit
service password-encryption
banner motd #your message here#
interface vlan 1
ip address {ip} {mask}
no shutdown
exit
ip default-gateway {gateway}
end
copy running-config startup-config
```

Packet Tracer sometimes skips lines when you paste a lot at once. If something is missing, paste a few lines at a time or type them one by one, then run `show running-config` to check.

### What each prompt means

| Prompt | Where you are | How to get out |
|--------|---------------|----------------|
| `Switch>` | User EXEC (you can't change anything) | Type `enable` to go up |
| `Switch#` | Privileged EXEC | Type `configure terminal` to start configuring |
| `Switch(config)#` | Global config | Type `end` to go back to `#` |
| `Switch(config-line)#` | Setting up a line (console or VTY) | Type `exit` to go back one level, or `end` to go all the way |
| `Switch(config-if)#` | Setting up an interface | Type `exit` to go back one level, or `end` to go all the way |

If you ever get lost, type `end`. That always takes you back to `Switch#`.

### Fixing mistakes

| What went wrong | How to fix it |
|-----------------|---------------|
| Typed a password wrong | Go back to that spot and type the command again with the right password. It replaces the old one. |
| Typed the hostname wrong | Type `hostname` again with the right name, in config mode. |
| Typed the IP address wrong | Go into `interface vlan 1` and type `ip address` again with the right address and mask. |
| Want to remove something | Put `no` in front of the command. For example, `no banner motd` or `no enable secret`. |
| Typed a command in the wrong mode | Type `end`, then `configure terminal`, and go back into the right spot. |
| Don't know what to type next | Type `?` to see your choices. |
| Saw `% Invalid input detected` | There is a typo. The `^` points to where it is. Press the Up Arrow, fix it, and press Enter. |
| Want to start completely over | At `Switch#`, type `erase startup-config`, press Enter, then type `reload` and answer no to saving. |

### Typing tips

- Press **Tab** after typing part of a command to finish it.
- Press the **Up Arrow** to bring back your last command and fix a typo.
- Use the short versions: `conf t`, `int vlan 1`, `sh run`, `copy run start`.
- Press **Ctrl+Z** to jump back to `Switch#` from anywhere.

### Final checklist

- [ ] Hostname set (the prompt shows the new name)
- [ ] Enable secret set (with `enable secret`, not `enable password`)
- [ ] Console password and `login` set
- [ ] VTY password and `login` set
- [ ] `service password-encryption` turned on
- [ ] MOTD banner set
- [ ] VLAN 1 IP address and mask set, and `no shutdown` run
- [ ] Saved with `copy running-config startup-config`
- [ ] Checked with `show running-config` and `show ip interface brief`
- [ ] Logged out and back in with both passwords to test them
- [ ] The PC has an IP address and the right subnet mask, and `ping` works

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

A new switch has no IP address, so the console is the only way to configure it the first time. To use the console you need a terminal program like PuTTY, Tera Term, or SecureCRT on your computer. In Packet Tracer, the PC's Terminal app does this job.

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
```

The running config lives in RAM and disappears on reboot. The startup config is saved in NVRAM and loads when the switch starts, so save after every set of changes. You can also type the short version, `copy run start`, and it does the same thing. Check what is saved with `show startup-config`.

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

### In Packet Tracer: Step 1

1. Open a console connection. Click the PC that has the console cable, go to **Desktop**, then **Terminal**, click **OK**, and press Enter. (If your lab does not say to use the console, you can use the switch's **CLI** tab instead.)
2. You start at `Switch>`. Type the commands from this step one at a time and press Enter after each one.
3. Packet Tracer fills in commands when you press Tab, and `?` works the same as on a real switch.
4. Once you set passwords, the switch asks for them the next time you log in:

| What you see | What to type |
|--------------|--------------|
| `User Access Verification`, then `Password:` | The **console line password** (the one you set under `line console 0`) |
| `Password:` right after you type `enable` | The **enable secret** |

Nothing shows on the screen while you type a password. That is normal.

5. Passwords are case-sensitive. If you mistype one (for example, a lowercase letter where a capital belongs), you don't have to delete anything. Go back to the right line, run the `password` command again with the correct one, and it replaces the old one.
6. To check your work, run `show running-config`. With `service password-encryption` on, the line passwords show up as scrambled text like `password 7 08...`, and the enable secret shows as `secret 5 $1$...` or similar.
7. `security passwords min-length 10` makes the switch refuse any password shorter than 10 characters. Only use it when your passwords are long enough. Some labs use short passwords (like a 5-character one), and with that command on, the switch would reject them.
8. When you save, the switch asks `Destination filename [startup-config]?`. Press Enter and wait for `[OK]`.
9. Don't mix up the **display name** (the label on the workspace) and the **hostname** (the name in the CLI prompt). The `hostname` command is the one that matters for grading.

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

### In Packet Tracer: Step 2

**On the switch:**

1. Run the `interface vlan 1` commands from this step, including `no shutdown`.
2. Run `show ip interface brief`. The `Vlan1` line should show the IP address and say `up` and `up`.
3. If `Vlan1` is down, check the link lights. The switch only brings VLAN 1 up when at least one port is connected and green. Click the fast-forward button to speed it up.

**On each PC:**

1. Click the PC, go to **Desktop**, then **IP Configuration**.
2. Pick **Static**.
3. Type the **IP Address**.
4. Click in the **Subnet Mask** box and check it. Packet Tracer fills this in by itself and it can be wrong (see the warning in Packet Tracer Basics). Fix it to match your addressing table.
5. Leave **Default Gateway** blank if your table doesn't have one.
6. Close the window. It saves on its own.

**Test it:** on a PC, open **Desktop**, then **Command Prompt**, and run `ipconfig` to see your settings. Then try `ping` with the other PC's address and the switch's address. The first ping often shows one `Request timed out` while the devices learn each other's addresses. The rest should reply.

If you ping the switch and it fails, the switch doesn't have its address yet. That is the most common reason, so go back and check `show ip interface brief`.

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

### In Packet Tracer: Step 3

- The 2960 switch in Packet Tracer has `FastEthernet0/1` to `FastEthernet0/24` (short name `f0/1`) and two `GigabitEthernet` ports. Use the port your PC is plugged into.
- A PC's network card is `FastEthernet0`, which runs at 100 Mbps. To match the switch settings on the PC side, click the PC, go to the **Config** tab, click **FastEthernet0**, and set **Bandwidth** to 100 Mbps and **Duplex** to Full Duplex.
- When you change speed or duplex, the link light may flash red or amber for a moment and come back green. Click the fast-forward button if you don't want to wait.
- Check the result with `show interfaces f0/1`. It shows the speed, the duplex, and any errors.
- If you just want it to work, leave both ends on `auto`. Mismatched settings are what cause trouble, not auto.

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

### In Packet Tracer: Step 4

- The `crypto key generate rsa modulus 1024` command takes a few seconds in Packet Tracer. Wait for the prompt to come back.
- If you leave off `modulus 1024`, the switch asks `How many bits in the modulus [512]:`. Type `1024` and press Enter. (SSH version 2 needs at least 768, so don't use the 512 default.)
- SSH works over the network, so you don't need the console cable for this part. The PC just needs to be on the same network as the switch and have an IP address.

**Connect from a PC (option 1, Command Prompt):**

1. Click the PC, go to **Desktop**, then **Command Prompt**.
2. Type `ssh -l admin 192.168.1.2` (use your own username and switch address).
3. Enter the password for that user. If you get the `S1>` prompt, SSH works.
4. Type `enable` and the enable secret to get to Privileged EXEC.

**Connect from a PC (option 2, Telnet / SSH Client):**

1. Click the PC, go to **Desktop**, then **Telnet / SSH Client**.
2. Set the **Connection Type** to **SSH**, type the switch's IP address and the username, and click **Connect**.
3. Enter the password when it asks.

**Check that Telnet is blocked:** from the PC Command Prompt, type `telnet 192.168.1.2`. After you run `transport input ssh`, it should fail to connect. That means only SSH is allowed.

If SSH won't connect, check that the switch has an IP address (`show ip interface brief`), that the domain name and username are set, that the RSA keys exist, and that the VTY lines have `transport input ssh` and `login local`.

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

### In Packet Tracer: Step 5

**Quick checks in Packet Tracer:**

- Look at every cable. All the link lights should be green.
- On every switch, run `show ip interface brief` and `show running-config`.
- On every PC, run `ipconfig`, then `ping` the other devices.
- In an activity file, click **Check Results** in the PT Activity window. Open the **Assessment Items** tab to see exactly which items are right and which are wrong.

**Packet Tracer troubleshooting**

| Problem | Likely cause | Fix |
|---------|-------------|-----|
| A link light is red | Wrong port, a device is off, or the port is shut down | Check the cable ends, turn the device on in the **Physical** tab, and run `no shutdown` on the port |
| A link light is amber | The switch port is still starting up | Wait about 30 seconds or click fast-forward |
| The Terminal window is blank | The console cable is missing or on the wrong port | Use the Console cable from `RS 232` on the PC to `Console` on the switch, then press Enter |
| `User Access Verification` asks for a password | You already set a console password | Type the console line password (nothing shows as you type) |
| The password is rejected | It has the wrong capital letters, or it was typed wrong when it was set | Passwords are case-sensitive. Re-run the `password` command with the right one on that line |
| First ping times out | The devices are still learning each other's addresses | Run the ping again. The rest should reply |
| Every ping fails | Wrong IP, wrong subnet mask, or the switch has no address yet | Check `ipconfig` on the PC and `show ip interface brief` on the switch |
| Check Results is under 100% | One or more items are wrong | Open **Assessment Items** and fix every item with a red X |
| Config is gone after reopening the file | You didn't run `copy run start` before saving | Run it on every switch, then save the Packet Tracer file with Ctrl+S |
| Subnet mask is wrong on a PC | Packet Tracer filled in the mask by itself | Click in the Subnet Mask box and change it to what the table says |

**Do not click Reset Activity** in a graded activity unless you want to start over. It wipes everything you set up.

## Packet Tracer Lab: Activity 2.9.1

This is the full walkthrough for the Packet Tracer activity "Basic Switch and End Device Configuration." It uses everything from Steps 1 and 2 above.

**Scenario:** you are a new LAN technician and need to set up a small LAN with two switches and two PCs. The network is already cabled and powered on.

### Topology

```
User-01 ---- ASw-1 ---- ASw-2 ---- User-02
```

### Addressing table

| Device | Interface | IP address | Subnet mask |
|--------|-----------|------------|-------------|
| ASw-1 | VLAN 1 | 128.107.20.10 | 255.255.255.0 |
| ASw-2 | VLAN 1 | 128.107.20.15 | 255.255.255.0 |
| User-01 | NIC | 128.107.20.25 | 255.255.255.0 |
| User-02 | NIC | 128.107.20.30 | 255.255.255.0 |

There is no default gateway in this table, so leave the gateway blank on the PCs.

### Passwords for this lab

| Password | Where it is used |
|----------|------------------|
| `R4Xe3` | The console line and the VTY lines (the activity says to use it for "all lines") |
| `C4aJa` | The enable secret |

Both are case-sensitive. `R4Xe3` has a capital R and a capital X. These are the example passwords the activity gives, not real ones.

### What the activity asks for and the command for each

| Requirement | How to do it |
|-------------|--------------|
| Use a console connection to access each switch | Console cable from User-01 to ASw-1 and from User-02 to ASw-2, then **Desktop**, then **Terminal** on the PC |
| Name ASw-1 and ASw-2 | `hostname ASw-1` and `hostname ASw-2` |
| Use the R4Xe3 password for all lines | `password R4Xe3` and `login` under `line console 0` and `line vty 0 15` |
| Use the C4aJa secret password | `enable secret C4aJa` |
| Encrypt all clear text passwords | `service password-encryption` |
| Configure an appropriate MOTD banner | `banner motd #Authorized access only!#` |
| Configure addressing for all devices | `interface vlan 1` addresses on the switches, and static IPs on the PCs |
| Save your configurations | `copy running-config startup-config` on both switches |
| Verify connectivity between all devices | `ping` from a PC |

The switches only have a console line and VTY lines (no AUX), so "all lines" means those two.

### 1. Make the console connections

1. Click the **Connections** icon (the lightning bolt) and pick the light blue **Console** cable.
2. Click **User-01** and choose `RS 232`. Then click **ASw-1** and choose `Console`.
3. Click the **Console** cable again. Click **User-02** and choose `RS 232`. Then click **ASw-2** and choose `Console`.

### 2. Configure ASw-1

Click **User-01**, go to **Desktop**, then **Terminal**, click **OK**, and press Enter. Then type:

```
enable
configure terminal
hostname ASw-1
line console 0
password R4Xe3
login
exit
line vty 0 15
password R4Xe3
login
exit
enable secret C4aJa
service password-encryption
banner motd #Authorized access only!#
interface vlan 1
ip address 128.107.20.10 255.255.255.0
no shutdown
end
copy running-config startup-config
```

Press Enter when it asks for the `startup-config` filename.

### 3. Configure ASw-2

Click **User-02**, go to **Desktop**, then **Terminal**, click **OK**, and press Enter. Then type:

```
enable
configure terminal
hostname ASw-2
line console 0
password R4Xe3
login
exit
line vty 0 15
password R4Xe3
login
exit
enable secret C4aJa
service password-encryption
banner motd #Authorized access only!#
interface vlan 1
ip address 128.107.20.15 255.255.255.0
no shutdown
end
copy running-config startup-config
```

If the switch asks for a password when you connect, it is the console line password (`R4Xe3`). After you type `enable`, it asks for the enable secret (`C4aJa`).

### 4. Set up the PCs

Click each PC, go to **Desktop**, then **IP Configuration**, and pick **Static**.

| PC | IP Address | Subnet Mask |
|----|-----------|-------------|
| User-01 | 128.107.20.25 | 255.255.255.0 |
| User-02 | 128.107.20.30 | 255.255.255.0 |

**Check the Subnet Mask box after typing the IP address.** Packet Tracer fills in `255.255.0.0` for these addresses, and that is wrong here. Change it to `255.255.255.0` on both PCs.

### 5. Verify connectivity

On User-01, open **Desktop**, then **Command Prompt**, and run:

```
ping 128.107.20.30
ping 128.107.20.10
ping 128.107.20.15
```

The first ping may time out once. The rest should reply. If the ping to a switch fails, that switch doesn't have its VLAN 1 address yet (see Step 2).

### 6. Check your score

In the PT Activity window, click **Check Results**, then open the **Assessment Items** tab. Check Results looks at these items:

| Device | Items checked |
|--------|---------------|
| ASw-1 and ASw-2 | Banner MOTD, console line password, enable secret, host name, VLAN 1 IP address, VLAN 1 port status, VLAN 1 subnet mask, service password-encryption, startup config, VTY line password |
| User-01 and User-02 | FastEthernet0 IP address and subnet mask |

Fix everything that shows a red X, then check again. A score under 100% means at least one of these is wrong.

### Mistakes to watch for

These are the ones that cost points in this lab:

- **Wrong subnet mask on the PCs.** Packet Tracer fills in `255.255.0.0` by itself. Change it to `255.255.255.0`.
- **A typo in the switch IP address.** The VLAN 1 address on ASw-1 has to be exactly `128.107.20.10`. Check it with `show ip interface brief`.
- **Wrong capital letters in `R4Xe3`.** Re-run the `password` command on the console and VTY lines to fix it.
- **Not saving.** Run `copy running-config startup-config` on both switches after the last change.
- **Using the CLI tab when the lab says to use a console connection.** Use the Terminal on the PC with the console cable.
- **Skipping `no shutdown` or not waiting for the green lights.** VLAN 1 won't come up without them.
- **Clicking Reset Activity.** This wipes everything you set up.

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

## Packet Tracer cheat sheet

| What you want to do | How to do it |
|---------------------|--------------|
| Add a switch | Network Devices, then Switches, then drag a 2960 onto the workspace |
| Add a PC | End Devices, then drag a PC onto the workspace |
| Connect two devices | Connections (lightning bolt), pick a cable, click the first device and a port, then the second device and a port |
| Configure a switch with a console connection | Console cable from the PC `RS 232` port to the switch `Console` port, then PC **Desktop**, then **Terminal**, then OK |
| Configure a switch without a cable | Click the switch, then the **CLI** tab |
| Set a PC's IP address | PC, then **Desktop**, then **IP Configuration**, then Static |
| Check the subnet mask | Look at the Subnet Mask box after typing the IP, because Packet Tracer fills it in |
| See a PC's address settings | PC **Desktop**, then **Command Prompt**, then `ipconfig` |
| Ping | PC **Desktop**, then **Command Prompt**, then `ping address` |
| Log into a switch with SSH | PC **Command Prompt**, then `ssh -l username address` |
| Make links turn green faster | Click the fast-forward button next to the Time clock |
| Watch packets move | Click **Simulation**, use **Add Simple PDU**, then **Capture / Forward** |
| Go back to normal | Click **Realtime** |
| Check an activity | PT Activity window, then **Check Results**, then the **Assessment Items** tab |
| Save the switch config | `copy run start` on each switch |
| Save the Packet Tracer file | Ctrl+S |  Save as