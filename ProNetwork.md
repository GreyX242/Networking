
# Routage Inter-VLAN

**1. Topologie – Vue d’ensemble**

```javascript
                +----------------------+
                |      Router0         |
                |   (DHCP + Fa0/0)     |
                +----------+-----------+
                           |
                           | Trunk (802.1Q)
                           |
                +----------+-----------+
                |  Core Switch (L3)   |
                | VTP Server + SVIs    |
                +----+-----------+-----+
                     |           |
               Trunk |           | Trunk
                +----+----+   +--+-----+
                | Dist1   |   | Dist2  |
                | VTP Cl. |   | VTP Cl.|
                +---------+   +--------+

```

2. Configuration du Core Switch (L3)

Créer les VLANs
```javascript
vlan 10
name SERVER
exit

vlan 20
name USERS
exit
 
vlan 30
name DMZ
exit

```

Activer VTP Server

```javascript
vtp domain atte-thm.cg
vtp version 2
vtp mode server
vtp password Password123@#
```

**Configurer les SVI (Interface VLAN) + Routage inter-VLAN**
OPTIONNEL

```javascript
interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit

interface vlan 30
ip address 192.168.30.1 255.255.255.0
no shutdown
exit

ip routing  

```

Configurer les ports trunks vers Router0 et les distributions

```javascript
interface gig0/1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,30
exit

interface gig0/2
switchport trunk encapsulation dot1q
switchport mode trunk
exit

interface gig0/3
switchport trunk encapsulation dot1q
switchport mode trunk
exit
```

# **3. Configuration des Switchs de Distribution (VTP Clients + Port Security)**

Mode VTP Client

```javascript
vtp domain atte-thm.cg
vtp version 2
vtp mode client
vtp password Password123@#
```

**Ports trunk vers le Core**

```javascript
interface gig0/1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,30
exit
```

### **Ports d’accès pour utilisateurs (exemple sur Dist1)**

VLAN 10 : serveurs (exemple)

````javascript
interface fastEthernet0/3
switchport mode access
switchport access vlan 10

````


#### VLAN 20 : utilisateurs

```javascript
interface fastEthernet0/2
switchport mode access
switchport access vlan 20
switchport port-security
switchport port-security maximum 2
switchport port-security violation restrict
switchport port-security mac-address sticky
exit
```

VLAN 30 : DMZ

```javascript
interface fastEthernet0/4
switchport mode access
switchport access vlan 30
```

# **4. Configuration du Routeur Router0**

###  **Interface Fa0/0 en trunk**
> Dans Packet Tracer, il faut sous-interfaces.

```javascript
interface fa0/0
no shutdown
```

Créer les sous-interfaces

```javascript
interface fa0/0.10
encapsulation dot1q 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

interface fa0/0.20
encapsulation dot1q 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit

interface fa0/0.30
encapsulation dot1q 30
ip address 192.168.30.1 255.255.255.0
no shutdown
exit
```

# **5. Activer DHCP sur Router0**

### → VLAN 10 (serveurs)

```javascript
ip dhcp excluded 192.168.10.1 192.168.10.9
ip dhcp pool VLAN10
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
```

### → VLAN 20 (Utilisateurs)

```javascript
ip dhcp excluded 192.168.20.1 192.168.20.19
ip dhcp pool VLAN10
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
```

### → VLAN 30 (DMZ)

```javascript
ip dhcp excluded 192.168.30.1 192.168.30.19
ip dhcp pool VLAN10
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
```

