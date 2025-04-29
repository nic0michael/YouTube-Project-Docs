---
title: "Firewalld Using firewall-cmd commands, Port Forwarding, Rich Rules, and NAT"
date: 2023-09-26T06:47:29Z
draft: false
---


**This is our Architecture :**
1. We have user nico on all the servers with the same password
2. server nat38 ip address 10.154.2.38
3. server nat40 ip address 10.154.2.40
4. server nat41 ip address 10.154.2.1
5. We am running rocky Linux 9 on the servers
5. Our workstation nmws runs DHCP ip address 10.154.2.89 now but will change

## 1. Find active zone and list it's rules'
```
sudo su -
firewall-cmd --get-active-zones

firewall-cmd --zone=public --list-all

sudo firewall-cmd --reload
```
### 1.1 disabeling and enabling iptables
**To disable ip tables**
```
sudo systemctl is-active iptables

sudo systemctl unmask iptables

```
**To enable iptables**
```
sudo systemctl start iptables
sudo systemctl enable iptables
sudo systemctl is-active iptables

```

## 2. Port Fowarding and Rich Rules port Fowarding 
With Rich Rules we can do more complex things

### 2.1 Use Port Forwarding to allow port 14022 on server nat38 to be used to ssh to server nat40
**To make this work we need to use Masquarading**
```
ssh nico@10.154.2.38
sudo su -
sudo firewall-cmd --reload
sudo systemctl status firewalld

# To view the name of default zone
firewall-cmd --get-default-zone

# To view the current configuration of default zone
firewall-cmd --list-all

firewall-cmd --zone=public --add-masquerade
```

```
sudo firewall-cmd --zone=public --list-forward-ports

sudo firewall-cmd --zone=public --add-forward-port=port=14022:proto=tcp:toport=22:toaddr=10.154.2.40

```
**We should be able from workstation nmws to ssh to server nat40 via server nat38**
```
ssh -p 14022 nico@10.154.2.38
```


**Destroying the rule**
```
sudo firewall-cmd --reload
sudo systemctl status firewalld
sudo firewall-cmd --zone=public --list-forward-ports

# if you have errors you need to reboot and repeat this step
reboot
```

### 2.2 Use Rich Rule Port Forwarding to allow port 14022 on server nat38 to be used to ssh to server nat40
**To make this work we need to use Masquarading**
```
ssh nico@10.154.2.38
sudo su -
sudo firewall-cmd --reload
sudo systemctl status firewalld
firewall-cmd --zone=public --add-masquerade
```

```
sudo firewall-cmd --zone=public --list-rich-rules

sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="0.0.0.0/0" forward-port port="14022" protocol="tcp" to-port="22" to-addr="10.154.2.40"'

sudo firewall-cmd --zone=public --list-rich-rules

sudo firewall-cmd --zone=public --query-rich-rule="xx"

```
**we should be able from workstation nmws to ssh to server nat40 via server nat38**
```
ssh -p 14022 nico@10.154.2.38
```

**Destroying the rule**
```
sudo firewall-cmd --reload
sudo systemctl status firewalld
sudo firewall-cmd --zone=public --list-rich-rules

# if you have errors you need to reboot and repeat this step
reboot
```


## 3. Rich Rules port Fowarding accross different private networks
We will change our Architecture to this :
1. We have user nico on all the servers with the same password
2. server nat38 ip address 10.154.2.38
3. server nat40 ip address 172.16.2.40
4. server nat41 ip address 172.16.2.41
5. We am running rocky Linux 9 on the servers
5. Our workstation nmws runs DHCP ip address 10.154.2.89 now but will change
we will only use Rich Rules this time
You will need a router to pass traffic between the two private networks 10.154.2.0 and 172.16.2.0

**To make this work we need Masquarading**
```
ssh nico@10.154.2.38
sudo su -
sudo firewall-cmd --reload
sudo systemctl status firewalld
firewall-cmd --zone=public --add-masquerade

```

```
sudo firewall-cmd --zone=public --list-rich-rules

sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="0.0.0.0/0" forward-port port="17240" protocol="tcp" to-port="22" to-addr="172.16.2.40"'

```
**we should be able from workstation nmws to ssh to server nat40 via server nat38**
```
ssh -p 17240 nico@10.154.2.38
```

**Destroying the rule**
```
sudo firewall-cmd --reload
sudo systemctl status firewalld
sudo firewall-cmd --zone=public --list-rich-rules
```

## A. The List commands

**These commands will help you manage and list various aspects of your Firewalld configuration.**


1. `firewall-cmd --zone=zone_name --list-all` [List all rules in a zone]
2. `firewall-cmd --permanent --zone=zone_name --list-all` [List all permanent rules in a zone]
3. `firewall-cmd --get-zones` [List available zones]
4. `firewall-cmd --get-default-zone` [Display the default zone]
5. `firewall-cmd --get-active-zones` [List active zones and their interfaces]
6. `firewall-cmd --zone=zone_name --list-services` [List services in a zone]
7. `firewall-cmd --zone=zone_name --list-ports` [List ports in a zone]
8. `firewall-cmd --zone=zone_name --list-sources` [List source addresses in a zone]
9. `firewall-cmd --zone=zone_name --list-rich-rules` [List rich rules in a zone]
10. `firewall-cmd --zone=zone_name --list-forward-ports` [List port forwarding rules in a zone]
11. `firewall-cmd --zone=zone_name --list-icmp-blocks` [List blocked ICMP types in a zone]
12. `firewall-cmd --zone=zone_name --list-rich-rules` [List custom rich rules in a zone]
13. `firewall-cmd --permanent --zone=zone_name --list-services` [List permanent services in a zone]
14. `firewall-cmd --permanent --zone=zone_name --list-ports` [List permanent ports in a zone]
15. `firewall-cmd --permanent --zone=zone_name --list-sources` [List permanent source addresses in a zone]
16. `firewall-cmd --permanent --zone=zone_name --list-rich-rules` [List permanent rich rules in a zone]
17. `firewall-cmd --permanent --zone=zone_name --list-forward-ports` [List permanent port forwarding rules in a zone]
18. `firewall-cmd --permanent --zone=zone_name --list-icmp-blocks` [List permanent blocked ICMP types in a zone]
19. `firewall-cmd --permanent --zone=zone_name --list-rich-rules` [List permanent custom rich rules in a zone]

## B How to add Rich Rules 
**`Command / Option/ Argument`	[Descriptions]**

1. **`firewall-cmd`** [The main command]
2. **`--add-rich-rule`**	[This option tells main command that we want to perform add operation]
3. **`rule`** [The rule keyword represents the starting point of the rule.   
Rules start with single quote followed by a rule keyword and end with a single quote ( ‘rule…………').]
4. **`-s 192.168.1.101/32`** [This is a filter condition which says apply the rule if packet is coming from source 192.168.1.101/24.   
Don't confuse with /32. It is not a subnet mask. It is a wildcard mask. In rich rules we use wildcard mask instead of subnet mask. To specify a particular host /32 wildcard mask is used.]
5. **`service name=telnet`** [This is also a filter condition which says apply the rule if packet is coming for telnet service]
6. **`limit value=1/m`** [This allows only one connection in a minute]
7. **`accept`** [To allow the packet if filter conditions are met.]


## C. Make a  Router to make a router allowing packets from iprange 10.154.2.0 to be routed to iprange 172.16.2.0
### 1. Enable IP forwarding:
**You will need a server with 2 network cards**

```
sudo nano /etc/sysctl.conf
```
**Find the line that says net.ipv4.ip_forward and change its value to 1 :**
```
net.ipv4.ip_forward = 1

```

**Save the file and apply the changes:**
```
sudo sysctl -p

```

### 2. Configure firewalld zones:
**You should have two zones set up, one for each subnet (e.g., zone1 for 10.154.2.0 and zone2 for 172.16.2.0).**

```
sudo firewall-cmd --permanent --new-zone=zone1
sudo firewall-cmd --permanent --zone=zone1 --change-interface=<your_interface1>

sudo firewall-cmd --permanent --new-zone=zone2
sudo firewall-cmd --permanent --zone=zone2 --change-interface=<your_interface2>

```
**Replace <your_interface1> and <your_interface2> with the actual network interfaces for each subnet.**

### 3. Define forwarding rules:
**Now, you need to define forwarding rules to allow packets to flow between these zones. Assuming zone1 corresponds to 10.154.2.0 and zone2 corresponds to 172.16.2.0:**

```
sudo firewall-cmd --permanent --zone=zone1 --add-masquerade
sudo firewall-cmd --permanent --zone=zone1 --add-forward-port=port=80:proto=tcp:toport=80:toaddr=172.16.2.0

sudo firewall-cmd --permanent --zone=zone2 --add-masquerade
sudo firewall-cmd --permanent --zone=zone2 --add-forward-port=port=80:proto=tcp:toport=80:toaddr=10.154.2.0

```
**These commands configure masquerading (NAT) and port forwarding rules for HTTP traffic. Adjust the port numbers and protocols as needed for your specific application.**

### 4. Reload the firewall configuration:
**After making these changes, reload the firewalld configuration to apply the new rules:**
```
sudo firewall-cmd --reload

```

Please note that this is a simplified example, and you may need to configure additional rules depending on the specific requirements of your network and the services you want to allow through the router. Additionally, ensure that the network devices in each subnet are properly configured to use the router as their gateway to reach the other subnet.



