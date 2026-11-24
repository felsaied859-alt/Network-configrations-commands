# RL Cheatsheets: Packet Tracer / Cisco IOS & Linux Networking

---

# Part 1: Cheatsheet Packet Tracer / Cisco IOS

## Mode Navigation

```
R> enable                              # enters Privileged EXEC mode
R# configure terminal                  # enters global config mode
R(config)# interface <type>/<number>   # enters interface config mode
```

Example:

```
interface fa0/1
```

## Tips and Tricks

| Command | Description |
|---|---|
| `?` | Displays all possible commands in the current mode |
| `<tab>` | Autocompletes the rest of the command |
| `do <command>` | Executes the command in Privileged EXEC mode, regardless of the current mode |
| `<shortcut>` | Type just the first letters of a command and press Enter |
| `<click Fast Forward Time>` | Speeds up the booting of the devices |
| `exit` | Exits the current mode |
| `end` | Exits the current mode and enters Privileged EXEC mode |
| `CTRL+SHIFT+6` | Interrupts the execution of the current command |
| `no <command>` | Cancels the command / deletes its configuration |

Example:

```
en
conf t
int fa0/0
```

## Show Commands

| Command | Description |
|---|---|
| `show running-config` | View the router's/switch's entire active configuration |
| `show ip interface brief` | View available interfaces and brief parameters (IP, status, etc.) |
| `show ip route` | View the routing table |
| `show mac-address-table` | View the CAM table |
| `show spanning-tree` | View spanning-tree (STP) parameters |
| `show vlan brief` | View VLAN parameters |
| `show interface vlan brief` | View VLAN brief parameters on interfaces |

## Basic Commands

### Add IPs (on router interfaces)

```
R(config)# interface <type>/<number>         # enters interface config mode
R(config-if)# ip address <IP> <decimal-MASK> # sets the IP and mask on the interface
R(config-if)# no shutdown                    # enables the interface (brings it up)
```

Example:

```
int fa0/3
ip add 10.10.10.1 255.255.255.248
no shut
```

## Spanning Tree Protocol

```
spanning-tree vlan <vlan-number> priority <value>
```

Sets the priority `<value>` of the switch for STP, per VLAN.

## VLAN Configuration (only on Switch)

### Mode ACCESS (interfaces connected to end-devices)

```
vlan <vlan-number>                       # creates the VLAN
interface <type>/<number>                # enters the interface to configure
switchport mode access                   # sets access mode
switchport access vlan <vlan-number>     # sets the access VLAN
```

Example:

```
vlan 10
int fa0/2
sw mo acc
sw acc vlan 10
```

### Mode TRUNK (interfaces connected to other switches or routers)

```
interface <type>/<number>                           # enters the interface to configure
switchport mode trunk                               # sets trunk mode
switchport trunk allowed vlan <vlan-number>|all     # sets which VLANs are allowed on the link
                                                    # (some VLANs, a range of VLANs, or all)
```

Example:

```
vlan 10
int fa0/1
sw mo tr
sw tr allowed vlan 10
```

or

```
sw tr allowed vlan all
```

or

```
sw tr allowed vlan 10-20
```

### Management VLAN (configuration on switch)

```
interface vlan <vlan-number>     # enters the VLAN interface
ip address <IP> <MASK>           # assigns the IP address and mask
PC> telnet <IP>                  # connects to the switch's IP
```

Example:

```
int vlan 99
ip add 10.10.10.99 255.255.255.0

PC> telnet 10.10.10.99
```

## CAM Table

```
mac-address-table static <MAC address> vlan <vlan-number> interface <type>/<number>
```

The MAC address is stored as static in the CAM table.

```
clear mac-address-table
```

Flushes the contents of the CAM table.

Example:

```
en
conf t
mac-address-table static 0001.6458.8b1a vlan 10 int fa0/1
```

## Routing Configuration

```
ip route <destination network> <destination network's mask> <next-hop>
```

Sets the route to the destination network through the next-hop.

Example:

```
en
conf t
ip route 10.10.10.0 255.255.255.0 192.168.0.1
```

Default route:

```
ip route 0.0.0.0 0.0.0.0 <next-hop>
```

All packets with unknown destinations are sent through that next-hop.

## Subnetting 101

**Topology:** R1 and R2 connected; Sw1 attached to R1 with hosts A (VLAN10, 126H) and B (VLAN20, 29H); host C (200H) attached to R2.

**Given network:** `192.168.0.0/22`

| Network | Hosts needed |
|---|---|
| R1R2 | 2H |
| VLAN10 | 126H |
| VLAN20 | 29H |
| R2C | 200H |

### Steps

1. Add the default gateway and 2 extra addresses (network + broadcast).
2. Write the networks in descending order.
3. Find the closest power of 2:
   - 200 + 1 + 2 <= 2^8
   - 126 + 1 + 2 <= 2^8
   - 29 + 1 + 2 <= 2^5
   - 2 + 2 <= 2^2
4. The power of 2 gives the mask: `32 - power -> /mask`
   - Example: power is 6, so mask is /26

### Result

| Network | Range |
|---|---|
| R2C | `192.168.0.0/24` -> `192.168.0.255/24` |
| VLAN10 | `192.168.1.0/24` -> `192.168.1.255/24` |
| VLAN20 | `192.168.2.0/27` -> `192.168.2.31/27` |
| R1R2 | `192.168.2.32/30` -> `192.168.2.35/30` |

---

# Part 2: Cheatsheet Linux - Networking

## VM Setup

Tutorial: <http://bit.ly/openstack_rl_tutorial>

```
ssh -o ServerAliveInterval=100 <ldap_user>@fep.grid.pub.ro
# connect to your fep account

ssh -i ~/.ssh/openstack.key student@<IP_virtual_machine>
# connect to the virtual machine you just created in OpenStack
```

Example:

```
ssh -o ServerAliveInterval=100 adi.minune@fep.grid.pub.ro
ssh -i ~/.ssh/openstack.key student@10.9.24.226
```

## Tips and Tricks

| Command | Description |
|---|---|
| `go [red\|green\|blue]` | Connect to one of the 3 containers |
| `lxc-list` | View the list of containers and their state |
| `rr [red\|green\|blue]` | Reboot one of the 3 containers |
| `<shortcut>` | Type just the first letters of a command and press Enter |
| `CTRL+a` -> press `q` | Exit the console of the container |
| `ping -c <value> <IP>` | Test connectivity to `<IP>` by sending `<value>` packets |

Example:

```
ping -c 2 10.10.0.1
```

## Basic Commands

### Add IPs

| Command | Description |
|---|---|
| `ip address add <IP>/<MASK> dev <interface>` | Sets the IP and mask on the interface |
| `ip address flush dev <interface>` | Resets the interface to its initial configuration |
| `ip link set dev <interface> up` | Enables the interface |
| `ip route add default via <IP-default-gateway>` | Sets the default gateway |
| `sysctl -w net.ipv4.ip_forward=1` | Activates routing / packet forwarding |

Example:

```
ip add add 192.168.0.1/24 dev veth-red
ip l s dev veth-red up
ip r a default via 10.0.0.1
```

## Show Commands

| Command | Description |
|---|---|
| `ip address show dev <interface>` | View layer 3 (network) configuration of the interface |
| `ip link show dev <interface>` | View layer 2 (data link) configuration of the interface |
| `ip route show` | View the routing table |
| `ip neighbor show` | View the ARP table |

Example:

```
ip a s dev eth0
ip l s dev veth-red
ip r s
```

## Network Services

### Remote Connection

| Command | Description |
|---|---|
| `ssh <username>@<IP/hostname> -p <port-number>` | Connect to `<username>` at the remote host via SSH on the given port |
| `ssh -l <username> <IP/hostname>` | Connect to `<username>` at the remote host via SSH |
| `ssh-keygen -t rsa` | Generate a public/private RSA key pair |
| `ssh-copy-id <username>@<IP/hostname>` | Copy the public key to the remote host for authentication |
| `telnet <IP/hostname>` | Connect via telnet |
| `ftp <IP/hostname>` | Connect via FTP |
| `scp -r <username>@<hostname>:<folder> .` | Download from `<username>` at `<hostname>` to your local host |
| `scp -r <file> <username>@<hostname>:` | Upload `<file>` from local host to `<username>` at `<hostname>` |

### Traffic Capture

**netcat**: arbitrary TCP and UDP connections and listeners

| Option | Description |
|---|---|
| `-l` | Listen (server) for connections |
| `-u` | Use UDP instead of the default TCP |

**netstat**: prints network connections

| Option | Description |
|---|---|
| `-t` | Lists TCP connections |
| `-l` | Lists services that listen on connections |
| `-u` | Lists UDP connections |

**dsniff**: captures network traffic and lists credentials when connections end

```
dsniff -i <interface>
```

Examples:

```
netcat -l 1234        # server that listens on TCP port 1234
netstat -tlnp         # lists the TCP services that listen on ports
dsniff -i eth0
```

## IPTABLES

```
iptables -t [table] [-A|-D|-I|-R|-L|-F] [chain] [options] [action]
```

### Tables

| Option | Description |
|---|---|
| `-t filter` | Filtering table (the default table) |
| `-t nat` | Altering table |
| `-t mangle` | Special altering table |

### Operations

| Option | Description |
|---|---|
| `-A` | Append rule to chain |
| `-D` | Delete rule |
| `-I <no>` | Insert as the given rule number |
| `-R` | Replace rule |
| `-L` | List all rules from given chain |
| `-F` | Flush the selected chain |

### Chains

**filter chains**

| Chain | Description |
|---|---|
| `INPUT` | Packets destined to the local host |
| `OUTPUT` | Locally-generated packets |
| `FORWARD` | Packets being routed through the local host |

**nat chains**

| Chain | Description |
|---|---|
| `PREROUTING` | Altering packets as soon as they come in |
| `POSTROUTING` | Altering packets as they are about to go out |
| `OUTPUT` | Altering locally-generated packets before routing |

### Match Options

| Option | Description |
|---|---|
| `-d <IP/hostname>` | Destination |
| `-s <IP/hostname>` | Source |
| `-p [tcp\|udp\|icmp\|all\|<number>]` | Protocol |
| `-i <input-interface>` | Input interface |
| `-o <output-interface>` | Output interface |
| `--dport <protocol/number>` | Destination port |
| `--sport <protocol/number>` | Source port |

### Actions

| Action | Description |
|---|---|
| `-j ACCEPT` | Lets through the packet that matched the options |
| `-j REJECT` | Rejects the packet that matched the options |
| `-j DROP` | Drops the packet, without sending an error notification |
| `-j DNAT` | Only in the nat table; modifies the destination address of the packet |

### Examples

View (list) rules and information on the filter table, FORWARD chain:

```
iptables -L FORWARD -n -v
```

Block FTP (port 21) from 20.20.0.1 to 10.10.0.1:

```
iptables -A INPUT -p tcp -d 10.10.0.1 --dport 21 -s 20.20.0.1 -j DROP
```

Redirect connections on port 22022 to SSH (port 22) of 10.0.0.1:

```
iptables -t nat -A PREROUTING -p tcp --dport 22022 -j DNAT --to-destination 10.0.0.1:22
```
