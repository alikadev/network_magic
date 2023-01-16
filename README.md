<style>
h1, h2 {
    margin-top: 30px;
}
h1, h2 {
    color: #f57171;
}
h3, h4, h5 {
    color: #de4545;
}
body {
    background-color: #222222;
}
li a{
    color: #f57171;
}
li:hover ol li {
    display: block;
}
li ol li {
    display: none;
}
li ol li a {
    color: #de4545;
}
p, ul {
    margin-left: 20px;
}
pre {
    background-color: #171717;
    padding: 10px;
    border-radius: 5px;
    margin: 20px;
    border: 2px solid #191919;
}
table{
    border-collapse:collapse;
}
table, table td{
    border: 1px solid #c0c0c0;
    padding-left: 8px;
    padding-right: 8px;
    padding-top: 3px;
    padding-bottom: 3px;
}
#rootref {
    position: fixed;
    scale: 2;
    text-decoration: none;
    bottom: 20;
    right: 20;
    background-color: #e6e6e6;
    color: #222;
    border-radius: 3px;
    width: 15px;
    height: 15px;
    text-align: center;
}
* {
    color: #e6e6e6;
}
</style>

<div id="root"></div>
[<a id="rootref" href="#root">^</a>](#root)

1. [RT SWI Cisco](#rt_swi_cisco)
    1. [Hostname](#rt_swi_cisco__hostname)
    1. [Domain](#rt_swi_cisco__domain)
    1. [Password](#rt_swi_cisco__password)
    1. [SSH](#rt_swi_cisco__ssh)
    1. [Telnet](#rt_swi_cisco__telnet)
    1. [Console](#rt_swi_cisco__con)
    1. [NTP](#rt_swi_cisco__ntp)
    1. [SNMP](#rt_swi_cisco__snmp)
    1. [Interface](#rt_swi_cisco__int)
    1. [Sub-Interfaces](#rt_swi_cisco__sub_int)
    1. [ACL in interface](#rt_swi_cisco__acl_set)
    1. [Route static](#rt_swi_cisco__route_static)
    1. [Default route](#rt_swi_cisco__default_route)
    1. [RIP](#rt_swi_cisco__rip)
    1. [EIGRP](#rt_swi_cisco__eigrp)
    1. [OSPF](#rt_swi_cisco__ospf)
    1. [Internet](#rt_swi_cisco__internet)
    1. [VPN](#rt_swi_cisco__vpn)
    1. [ACLs](#rt_swi_cisco__acl)
    1. [VLANs](#rt_swi_cisco__vlan)
    1. [Trunk](#rt_swi_cisco__trunk)
    1. [Distant Connection](#rt_swi_cisco__connection)
1. [FW Cisco](#fw_cisco)
    1. [Minimal config](#fw_cisco__minimal)
    1. [SSH](#fw_cisco__ssh)
    1. [NTP](#fw_cisco__ntp)
    1. [SNMP](#fw_cisco__snmp)
    1. [DHCP](#fw_cisco__dhcp)
    1. [HTTP](#fw_cisco__http)
    1. [Logs](#fw_cisco__log)
    1. [Interface](#fw_cisco__int)
    1. [Management interface](#fw_cisco__man_int)
    1. [Default route](#fw_cisco__default)
    1. [PAT](#fw_cisco__pat)
    1. [NAT](#fw_cisco__nat)
1. [STD EMF](#std_emf)
    1. [Domain](#std_emf__domain)
    1. [Passwords](#std_emf__passwords)
    1. [Users](#std_emf__users)

---

# Router / Switch Cisco <a name="rt_swi_cisco"></a>

## Basic configurations

### Hostname <a name="rt_swi_cisco__hostname"></a>

```
conf t
host <HostName>
end
```

### Domain <a name="rt_swi_cisco__domain"></a>

```
conf t
no ip domain lookup
ip domain-name <domain>
end
```

### Password <a name="rt_swi_cisco__password"></a>

```
conf t
enable pass <password>
end
```

### SSH <a name="rt_swi_cisco__ssh"></a>

REQ:

- Hostname
- Domain

```
conf t

crypto key generate rsa
1024

username <usernameSSH> password 0 <passwdSSH>
ip ssh authentication-retries 2
ip ssh version 2

line aux 0
 stopbits 1

line vty 0 4
 logging synchronous
 login local
 transport input ssh
 transport output ssh

ip ssh version 2
end
```

### Telnet  <a name="rt_swi_cisco__telnet"></a>

```
line vty 0 4
 transport input telnet
 password <passwdTELNET>
 login
```

### Console <a name="rt_swi_cisco__con"></a>

```
line con 0
 password <passwdCON>
 logging synchronous
 transport input none
 stopbits 1
 ```

### NTP <a name="rt_swi_cisco__ntp"></a>

```
conf t
ntp server 162.23.41.10
clock timezone WEST 1 0
clock summer-time WEST recurring
end
```

### SNMP <a name="rt_swi_cisco__snmp"></a>

```
conf t
Snmp-server community EMF_Public RO
Snmp-server community EMF_Private RW
snmp-server location "A42-09"
snmp-server contact "EMF/KuciE"
end
```

## Interfaces

### Define and Enable <a name="rt_swi_cisco__int"></a>

```
conf t
int <int>
ip addr <IPv4> <Mask>
desc <descInt>
no sh
end
```

### Sub-interfaces <a name="rt_swi_cisco__sub_int"></a>

```
conf t
int <int>
no sh
int <int>.<SubInterface>
encapsulation dot1Q <VlanID>
ip addr <IPv4> <Mask>
end
```

### Use ACL <a name="rt_swi_cisco__acl_set"></a>

```
conf t
int <int>
ip access-group <acl> <in/out>
end
```

## Routing

### Static <a name="rt_swi_cisco__route_static"></a>

```
conf t
ip route <subnet> <mask> <ip>
end
```

### Default route <a name="rt_swi_cisco__default_route"></a>

```
conf t
ip route 0.0.0.0 0.0.0.0 <ip>
end
```

### RIP <a name="rt_swi_cisco__rip"></a>

```
conf t
router rip
versions 2
do sh ip ro

net <C-Port>

end
```

### EIGRP <a name="rt_swi_cisco__eigrp"></a>

```
conf t
router eigrp <ID>
do sh ip ro

net <C-Subnet> <WildcardMask>

end
```

### OSPF <a name="rt_swi_cisco__ospf"></a>

#### Internal Router <a name="rt_swi_cisco__ospf_internal"></a>

```
conf t
router ospf <ospfID>

do sh ip ro
net <ConnectedSubnet> <WildcardMask> area <AreaID>

end
```

#### Border Router <a name="rt_swi_cisco__ospf_border"></a>

```
conf t
router ospf <ID>

do sh ip ro
net <ConnectedSubnet> <WildcardMask> area <AreaID>

area <AreaID> range <Subnets∑> <Mask>

end
```

### To Internet <a name="rt_swi_cisco__internet"></a>

```
conf t
int <intInternet>
ip nat outside

int <othersInterfaces>
ip nat inside

ip nat inside source list <alc> interface <intInternet> overload

end

sh ip nat translation
```

## VPNs <a name="rt_swi_cisco__vpn"></a>

```
! Create isakmp policy
conf t
 cry isakmp policy <PolicyID>
  encr 3des
  hash md5
  auth pre
  group 2
  lifetime <LifeTime>
  exit
 cry isakmp key <Passwd> address <PublicDestIP>
 
! Create VPN ALC
 ip access-list extended <ACLID>
  permit ip <SrcIP> <SrcWildmask> <DestIP> <DestWildmask>
  exit

! Add IPSec
 cry ipsec transform <TSName> esp-3des esp-md5-hmac
 cry map <MapName> <MapID> ipsec-isakmp
  set peer <DescIP>
  set transform <TSName>
  match address <ACLID>
  exit

! Add map in interface
 int <IntOUT>
  cry map <MapName>
  end
```

## ACLs <a name="rt_swi_cisco__acl"></a>

### Standard

```
conf t
ip access-list standard <1-99>

remark <remark>
permit <subnet> <wildcardMask>
deny host <ip>

end
```

### Extended

```
conf t
ip access-list standard <alc(>99)>

remark <reamark>
permit <transport> <subnetSrc> <wildcardMaskSrc> lt <portSrc> <subnetDest> <wildcardMaskDest> eq <protocol> 
deny <transport> host <ipSrc> host <ipDest> gt <protocol>

end
```

## Vlan <a name="rt_swi_cisco__vlan"></a>

### Define

```
conf t
vlan <vlanID>
name <vlanName>
end
```

### Put int Interface

```
conf t
int <interface>
switchport acc vl <vlanID>
end
```

### Put in Interface Range

```
conf t
int rande <intMIN> - <intMAC>
switchport acc vl <vlanID>
```

### Trunk <a name="rt_swi_cisco__trunk"></a>

Le trunk envoie tous les VLANs vers le routeur exterieur

```
conf t
int <int>
switchport mo tru
switchport trunk allowed vlan <vlanID>, ...
desc <desc>
no sh
end
```

## Distant connection <a name="rt_swi_cisco__connection"></a>

- Connect SSH

  - `ssh -l <userSSH> <ip>`

- Connect Telnet

  - `telnet <ip>`










# Firewall Cisco <a name="fw_cisco"></a>

## Basic config

### Hostname, Password, Domain <a name="fw_cisco__minimal"></a>

```
hostname <nomFW>
enable password <Password>
domain-name <Domain>
```

### SSH <a name="fw_cisco__ssh"></a>

```
crypto key generate rsa modulus 4096
yes
username <name> password <password>
aaa authentication ssh console LOCAL
ssh version 2
ssh timeout <timeout>
ssh <management host> <netmark> <interface name>
ssh stricthostkeycheck
banner motd 
```

### NTP <a name="fw_cisco__ntp"></a>

```
ntp server <servers... (195.186.4.101,84.16.73.33)>
clock timezone CET +1
```

### SNMP <a name="fw_cisco__snmp"></a>

```
snmp-server enable
snmp-server enable traps all
snmp-server location "<location>"
snmp-server contact "<contact (e-mail)>"
snmp-server community <community>
snmp-server host <interface name> <client ip addr> community <community name> version 2c
```

### DHCP <a name="fw_cisco__dhcp"></a>

```
no dhcpd auto_config <interface name>
dhcpd dns <DNS server #1> <DNS server #2>
dhcpd address <ip start>-<ip end> <interface name>
dhcpd enable <interface name>
```

### HTTP <a name="fw_cisco__dhcp"></a>

```
asdm image disk0:/asdm-782.bin
http server enable
http <subnet addr> <netmask> <interface>
``` 

### Logging <a name="fw_cisco__log"></a>

``` 
logging enable
logging timestamp
logging console <log severity>
logging buffered <log severity>
logging host <interface name> <host>
```

## Setup interface

### Normal interface <a name="fw_cisco__int"></a>

```
interface <int>
  nameif <interface name>
  security-level <sl>
  ip address <ip addr> <netmask>
  no shutdown
```

### Management interface <a name="fw_cisco__man_int"></a>

```
interface <int>
  management-only
  nameif <interface name>
  security-level 100
  ip address <ip addr> <netmask>
  no shutdown
```

## Routing 

### Default route <a name="fw_cisco__default"></a>

```
route <interface name> 0.0.0.0 0.0.0.0 192.168.1.1
```

### PAT <a name="fw_cisco__pat"></a>

```
object network <object name>
  subnet <ip addr output> <netmask>
  nat (<IN>,<OUT>) dynamic interface
```


### NAT <a name="fw_cisco__nat"></a>

```
object network <object name>
  host <inside ip addr>
  nat (<OUT>,<IN>) static <outside ip addr>
```

# Standards EMF <a name="std_emf"></a>

## Domain <a name="std_emf__domain"></a>

 - emf.local

## Passwords <a name="std_emf__passwords"></a>

| Type    | Password         |
| ------- | ---------------- |
| Enable  | `Pa$$w0rdena`    |
| Console | `Pa$$w0rdcon`    |
| SSH     | `Pa$$w0rdssh`    |
| Telnet  | `Pa$$w0rdtelnet` |
| VPN     | `Pa$$w0rdvpn`    |

## NTP Server <a name="std_emf__ntp"></a>

- `162.23.41.10`

## Name <a name="std_emf__users"></a>

- SSH: `adminSSH`
- SSH PKT: `adminssh`
