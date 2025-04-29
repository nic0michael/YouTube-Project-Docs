---
title: "Install Metasploit Framework on Mint / Ubuntu Linux or Kali Linux in Proxmox"
date: 2024-02-24T13:23:32Z
draft: false
---
## DISCLAIMER
We provide this information for training or doing White Hat Hacking \
This is not a Hacking training course, however the instructions provided will show you how to identify vulnerabilities in your servers \
DO NOT USE THIS ON NETWORKS THAT YOU ARE NOT AUTHORIZED TO ACCESS THAT IS ILLEGAL AND AGAINST THE SPIRIT OF ETHICAL HACKING.

## What is Metasploit
Metasploit is a Penetration Testing framework used by Ethical Hackers to find and verify the existance of Vulnerabilities on your Network
## 1. Creating Linux VM
### 1.1 Install Kali Linux
The Installation Binaries can be fould here:
[https://www.kali.org/get-kali/#kali-installer-images](https://www.kali.org/get-kali/#kali-installer-images) \
At the time of the recording of our video this was the current version : \
https://cdimage.kali.org/kali-2023.4/kali-linux-2023.4-installer-amd64.iso \
Create a VM using this ISO file

https://rpm.metasploit.com/

### 1.2 Install Mint Linux 21.3
Download the ISO from here into your Proxmox server: \
[https://www.linuxmint.com/edition.php?id=312](https://www.linuxmint.com/edition.php?id=312} \
Choose you Country and download the ISO file 

You may use this: \
https://mirrors.cicku.me/linuxmint/iso/stable/21.3/linuxmint-21.3-mate-64bit.iso \
Create a VM using this ISO file

## 2. Installing the Metasploit-Framework

### 2.1 Official instructions to install Metasploit Framework on Linux

Run these commands:
```
mkdir /opt/metasploit

cd /opt/metasploit


curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && chmod 755 msfinstall && ./msfinstall

ls -la
```

Expect
```
-rwxr-xr-x  1 root  root  6144 Mar 29 17:42 msfinstall
```

Run these commands
```
cd /opt/metasploit-framework/bin

./msfconsole

# When prompted to setup a database  reply Yes
```

Run this command
```
db_status
```
Expect to get
```
[*] postgresql connected to msf
```



**Much Simpler method using Snap for Ubuntu Server 2204** \
Run this command
```
snap install metasploit-framework
```
Expect to get :
```
metasploit-framework v6.4.1-dev from Jitendra Patro (jitpatro) installed

```

### 2.2 Install Metasploit in a Docker Container
Run this command in the terminal
```
docker run --rm -it \
       -p 4444:4444 -p 80:80 -p 8080:8080 \
       -p 443:443 -p 445:445 -p 8081:8081 \
       strm/metasploit
```

It will install Metasploit in a docker container and run it in the terminal
Just wait for the PostgreSQL Database to start it takes 1 minute

You may want to create a Shell Script to start Metaspploit when you need it \
Run these commands in the terminal :
```
nano /usr/bin/start-metasploit
```
Put this in the file:
```
#!/bin/bash

docker run --rm -it \
       -p 4444:4444 -p 80:80 -p 8080:8080 \
       -p 443:443 -p 445:445 -p 8081:8081 \
       strm/metasploit

```
Run this command to make the shell script executable :
```
chmod 775 /usr/bin/start-metasploit
```
Ane now you can run:"
```
start-metasploit
```
Just wait for the PostgreSQL Database to start it takes	1 minute

## 3. Penetration Testing using nmap

### 3.1 NMAP Scan Types Explained:

1. ** SYN Scan (-sS):**
Example: nmap -sS 10.154.2.1/24 
This stealthy scan method is used to find devices within the IP range 10.154.2.1/24.
2. **TCP Connect Scan (-sT):**
Example: nmap -sT 10.154.2.1/24 \
This method completes the full connection process, useful for scanning all devices within the specified IP range.
3. **Operating System Detection (-O):**
Example: nmap -O 10.154.2.1/24 \
Determines the operating system of devices within the IP range by analyzing their responses.
4. **Version Detection (-sV):**
Example: nmap -sV 10.154.2.1/24 \
Reveals the versions of services and applications running on devices within the specified IP range.
5. **Port Specification (-p):**
Example: nmap -p22,25 10.154.2.1/24 \
Scans only the SSH (port 22) and SMTP (port 25) ports on devices within the IP range.
6. **No Ping Scan (-Pn):**
Example: nmap -Pn 10.154.2.1/24 \
Skips the ping step and assumes all devices within the IP range are online before scanning.
7. **Timing and Performance (-T):**
Example: nmap -T4 10.154.2.1/24 \
Controls the aggressiveness of the scan. Here, -T4 indicates a moderately fast scan.
8. **ACK Scan (-sA):**
Example: nmap -sA 10.154.2.1/24 \
Helps identify filtered or unfiltered firewall ports within the IP range.
9. **XMAS Scan (-sX):**
Example: nmap -sX 10.154.2.1/24 \
Sends specific flags to devices within the IP range to detect ports, resembling Christmas lights.
10. **Verbose Mode (-v):**
Example: nmap -v 10.154.2.1/24 \
Provides more detailed output during the scan of devices within the specified IP range.
11. **Input File (-iL):**
Example: nmap -iL target_devices.txt 

Scans targets listed in a text file (target_devices.txt) that includes the following IP addresses: \
10.154.2.1 \
10.154.2.3 \
10.154.2.87 \
10.154.2.89
### 3.3 Nmap Scan Examples

1 SYN Scan (-sS): scanned in 9.14 seconds
```
nmap -sS 10.154.2.1-46
```

2 Operating System Detection (-O): scanned in 14.59 seconds
```
nmap -O 10.154.2.1-46
```
3 Version Detection (-sV): scanned in 56.79 seconds
```
nmap -sV 10.154.2.1-46
```

4 Port Specification (-p): scanned in 2.25 seconds
```
nmap -p 21,22,23,25,53,80,110,143,443,465,587,993,995,3389,5432,5900,8080 10.154.2.1-46
```
5 No Ping Scan (-Pn): scanned in 9.11 seconds
```
nmap -Pn 10.154.2.1-46
```
#### 3.3.1 nmap scan using file of IP Addresses
Now create this file:
```
mkdir /opt/nmap-target-devices

cd /opt/nmap-target-devices

nano target_devices.txt
```
Put this in the file:
```
10.154.2.1
10.154.2.3
10.154.2.45
10.154.2.46
```
6 SYN Scan (-sS) with Input File (-iL): scanned in 6.92 seconds
```
nmap -sS -iL target_devices.txt
```

7 Operating System Detection (-O) with Input File (-iL): scanned in 11.00 seconds
```
nmap -O -iL target_devices.txt
```

8 Version Detection (-sV) with Input File (-iL): scanned in 53.46 seconds
```
nmap -sV -iL target_devices.txt
```

9 Port Specification (-p) with Input File (-iL): scanned in 1.38 seconds
```
nmap -p 22,80,443 -iL target_devices.txt 
```
## 4. Penetration Testing using Metasploit

### 4.1. Network discovery (to discover devices on your network) scanned in 11.78 seconds
sudo nmap -sS <Your Subnet>
sudo nmap -sS 10.154.2.0/24

ignore gateways 10.154.2.1 10.154.2.3

### 4.2 Use identified machines
Now select the IPs you found excluding the above
put them in this file
```
nano metasploit-devices.txt
```
add these files here
```
10.154.2.40
10.154.2.46
10.154.2.3
```
Identify the O/S for these IPs in our List scanned in 10.61 seconds
```
sudo nmap -O -iL target_devices.txt
```

Scripts in nmap.org:
https://nmap.org/nsedoc/lib/nmap.html

### 4.3 Test these machines
Add metasploit target machine
```
nano metasploit-devices.txt
```
add these files here
```
10.154.2.40
10.154.2.3
```

Now scan this machine scanned in 113.42 seconds
```
sudo nmap --script vuln -iL metasploit-devices.txt
```

you should get vulnerabilities (something like smb-vuln-ms08-067)
make a list of all these vulnerabilities for all your servers
poodle is a man in the middle

### 4.4 Enable Metasploit

Before you run Metasploit you need to start it PostGresQL DB

service postgresql start (kali linux equivalent)

### 4.5 Exploit machines
```
sudo msfconsole
```


```
search <the vulnr found >
```


you need to look for an exploit module to use

looks like this \
1 exploit/OS/service/vuln \
2 exploit/OS/service/vuln \
3 scanner/OS/service/vuln 

we will use a scanner module to get info

```
use 3 
options
```


fill in the required info (for everthing that has Required) to do the Test

set ehosts 10.154.2.89

```
run
```

to exploit we will now choose the exploit module \
use 2 (assume this is the option) \
```
options
```

fill in the required info (for everthing that has Required) to do the Test
```
exploit
```


We now get a shell 
```
sysinfo
whoami
dir 
pwd
exit
```

## 5. Doing a network scan with Msploit

 **1. Start Metasploit:** \
Open a terminal window and start Metasploit by running the following command:

```
msfconsole
```
 **2. Search for the IP range scanner module:** \
Inside the Metasploit console, you can search for the IP range scanner module using the search command:

```
search ip_range
```
 **3. Select the IP range scanner module:** \
Once you've identified the appropriate module, you can load it using the use command followed by the module path. For example:

```
use auxiliary/scanner/ip/ip_range
```
 **4. Set the options:** \
Before running the module, you need to set the target IP range. Use the show options command to view the required options and set them accordingly. In this case, you'll need to set the RHOSTS option to the IP range you want to scan. For example:

```
set RHOSTS 10.154.2.3-10.154.2.50
```
 **5. Run the scan:** \
After setting the options, you can run the scan by typing run and pressing Enter:

```
run
```
 **6. Review the results:** \
Once the scan completes, you'll see the results in the console. Metasploit will display information about the scanned hosts, such as open ports and services.
## 6. Doing a Penetration Test with Metasploit

 **1. Start Metasploit:** \
Open a terminal window and start Metasploit by running the following command:
```
msfconsole
```
 **2. Search for vulnerability scanning modules:** \
Inside the Metasploit console, you can search for modules related to vulnerability scanning. You can use the search command with keywords like scanner, vuln, or audit:
```
search scanner
```

 **3. Select a scanner module:** \
Choose a scanner module appropriate for your target. For example, you might use the auxiliary/scanner/portscan/tcp module to perform a TCP port scan on the target VM. Load the module using the use command:

```
use auxiliary/scanner/portscan/tcp
```
 **4. Set the options:** \
Before running the module, you need to set the target IP address. Use the show options command to view the required options and set them accordingly. In this case, you'll need to set the RHOSTS option to the IP address of your Proxmox VM:

```
set RHOSTS 10.154.2.48
```

 **5. Run the scan:** \
After setting the options, you can run the scan by typing run and pressing Enter:

```
run
```
 **6. Review the results:** \
Once the scan completes, Metasploit will display the results of the scan, including open ports and potentially vulnerable services.


## 7. Running a port scanner
Type these commands
```
sudo su -

msfconsole

show auxiliary

# now find the scan you want to run we are looking for : auxiliary/scanner/portscan/tcp 
use auxiliary/scanner/portscan/tcp

# Display the options
show options

# Set options like this : set [option name] [option value]
set RHOST 10.154.2.89

# Change the number of threads by running
set THREAD 50

# Verify you have the desired settings
info

# Run the scan
run
```




## Bibliography
https://docs.rapid7.com/metasploit/installing-the-metasploit-framework/ \
https://www.alibabacloud.com/blog/what-is-metasploit-how-to-install-metasploit-on-ubuntu_599955 \
https://thecybersecurityman.com/2018/09/27/pentest-edition-go-from-beginner-to-advanced-with-nmap/ \
https://www.youtube.com/watch?v=Keld6Wi8aZ4&list=PLg1ikb8zEVCnLp45Se9YtIeUSmHfGTrYS&index=72 \
https://www.youtube.com/watch?v=QynUOJanNqo&list=PLg1ikb8zEVCnLp45Se9YtIeUSmHfGTrYS&index=71 \
https://www.youtube.com/watch?v=U7e-mcJdZok&list=PLg1ikb8zEVCnLp45Se9YtIeUSmHfGTrYS&index=73&t=1083s \
https://github.com/opsxcq/docker-metasploit?tab=readme-ov-file
