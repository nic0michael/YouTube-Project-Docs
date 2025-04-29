---
title: "We Compare Zerotier Tailscale and Netbird"
date: 2024-08-09T10:37:08Z
draft: false
---
Although Netbird is most popular it is not the best performing System according to our results. \
That title goes to Zerotier


## Issue Installing Tailscale on Rocky Linux server after Zerotier was installed

```
Downloading Packages:
[MIRROR] tailscale_1.70.0_x86_64.rpm: Curl error (28): Timeout was reached for https://pkgs.tailscale.com/stable/rhel/9/x86_64/tailscale_1.70.0_x86_64.rpm [Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds]
[MIRROR] tailscale_1.70.0_x86_64.rpm: Curl error (28): Timeout was reached for https://pkgs.tailscale.com/stable/rhel/9/x86_64/tailscale_1.70.0_x86_64.rpm [Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds]

```

## 1. Test 1  : Ping Rino server in the USA from Laptop
```
# Using Zerotier connection : Average : 261.767 mS
nickm@dell-laptop:~$ ping 10.244.24.218
PING 10.244.24.218 (10.244.24.218) 56(84) bytes of data.
64 bytes from 10.244.24.218: icmp_seq=1 ttl=64 time=258 ms
64 bytes from 10.244.24.218: icmp_seq=2 ttl=64 time=252 ms
64 bytes from 10.244.24.218: icmp_seq=3 ttl=64 time=255 ms
64 bytes from 10.244.24.218: icmp_seq=4 ttl=64 time=257 ms
64 bytes from 10.244.24.218: icmp_seq=5 ttl=64 time=252 ms
64 bytes from 10.244.24.218: icmp_seq=6 ttl=64 time=259 ms
64 bytes from 10.244.24.218: icmp_seq=7 ttl=64 time=254 ms
64 bytes from 10.244.24.218: icmp_seq=8 ttl=64 time=317 ms
64 bytes from 10.244.24.218: icmp_seq=9 ttl=64 time=252 ms
--- 10.244.24.218 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8012ms
rtt min/avg/max/mdev = 252.329/261.767/316.547/19.512 ms

# Direct connection :  Average : 271.427
nickm@dell-laptop:~$ ping 50.116.50.124
PING 50.116.50.124 (50.116.50.124) 56(84) bytes of data.
64 bytes from 50.116.50.124: icmp_seq=1 ttl=47 time=279 ms
64 bytes from 50.116.50.124: icmp_seq=2 ttl=47 time=302 ms
64 bytes from 50.116.50.124: icmp_seq=3 ttl=47 time=253 ms
64 bytes from 50.116.50.124: icmp_seq=4 ttl=47 time=253 ms
64 bytes from 50.116.50.124: icmp_seq=5 ttl=47 time=255 ms
64 bytes from 50.116.50.124: icmp_seq=6 ttl=47 time=254 ms
64 bytes from 50.116.50.124: icmp_seq=7 ttl=47 time=310 ms
64 bytes from 50.116.50.124: icmp_seq=8 ttl=47 time=253 ms
64 bytes from 50.116.50.124: icmp_seq=9 ttl=47 time=284 ms
--- 50.116.50.124 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8011ms
rtt min/avg/max/mdev = 252.806/271.427/310.180/21.717 ms
```

## 2. Test 2 : Ping Proxmox server from laptop
```
# Direct connection : Average : 1.501 mS
nickm@dell-laptop:~$ ping upupa
PING upupa (10.154.2.89) 56(84) bytes of data.
64 bytes from upupa (10.154.2.89): icmp_seq=1 ttl=64 time=1.37 ms
64 bytes from upupa (10.154.2.89): icmp_seq=2 ttl=64 time=2.85 ms
64 bytes from upupa (10.154.2.89): icmp_seq=3 ttl=64 time=1.26 ms
64 bytes from upupa (10.154.2.89): icmp_seq=4 ttl=64 time=1.31 ms
64 bytes from upupa (10.154.2.89): icmp_seq=5 ttl=64 time=1.28 ms
64 bytes from upupa (10.154.2.89): icmp_seq=6 ttl=64 time=1.28 ms
64 bytes from upupa (10.154.2.89): icmp_seq=7 ttl=64 time=1.40 ms
64 bytes from upupa (10.154.2.89): icmp_seq=8 ttl=64 time=1.42 ms
64 bytes from upupa (10.154.2.89): icmp_seq=9 ttl=64 time=1.34 ms
--- upupa ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8014ms
rtt min/avg/max/mdev = 1.257/1.501/2.852/0.480 ms

# Zerotier connection :  Average : 8.563 mS
nickm@dell-laptop:~$ ping 10.244.67.30
PING 10.244.67.30 (10.244.67.30) 56(84) bytes of data.
64 bytes from 10.244.67.30: icmp_seq=1 ttl=64 time=13.1 ms
64 bytes from 10.244.67.30: icmp_seq=2 ttl=64 time=3.49 ms
64 bytes from 10.244.67.30: icmp_seq=3 ttl=64 time=1.83 ms
64 bytes from 10.244.67.30: icmp_seq=4 ttl=64 time=1.79 ms
64 bytes from 10.244.67.30: icmp_seq=5 ttl=64 time=48.3 ms
64 bytes from 10.244.67.30: icmp_seq=6 ttl=64 time=3.18 ms
64 bytes from 10.244.67.30: icmp_seq=7 ttl=64 time=1.85 ms
64 bytes from 10.244.67.30: icmp_seq=8 ttl=64 time=1.62 ms
64 bytes from 10.244.67.30: icmp_seq=9 ttl=64 time=1.95 ms
--- 10.244.67.30 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8013ms
rtt min/avg/max/mdev = 1.622/8.563/48.275/14.454 ms

```

## 3. Test 3 : From our Proxmox server we run this test on Tailscale VM: 
```
# Direct Connection : Average : 0.249 mS
ping 10.154.2.86
PING 10.154.2.86 (10.154.2.86) 56(84) bytes of data.
64 bytes from 10.154.2.86: icmp_seq=1 ttl=64 time=0.168 ms
64 bytes from 10.154.2.86: icmp_seq=2 ttl=64 time=0.217 ms
64 bytes from 10.154.2.86: icmp_seq=3 ttl=64 time=0.203 ms
64 bytes from 10.154.2.86: icmp_seq=4 ttl=64 time=0.243 ms
64 bytes from 10.154.2.86: icmp_seq=5 ttl=64 time=0.225 ms
64 bytes from 10.154.2.86: icmp_seq=6 ttl=64 time=0.207 ms
64 bytes from 10.154.2.86: icmp_seq=7 ttl=64 time=0.217 ms
64 bytes from 10.154.2.86: icmp_seq=8 ttl=64 time=0.205 ms
64 bytes from 10.154.2.86: icmp_seq=9 ttl=64 time=0.249 ms
--- 10.154.2.86 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8189ms
rtt min/avg/max/mdev = 0.168/0.214/0.249/0.022 ms

# Tailscale connection : Average : 0.591 mS
nickm@upupu:~$ ping 100.92.87.31
PING 100.92.87.31 (100.92.87.31) 56(84) bytes of data.
64 bytes from 100.92.87.31: icmp_seq=1 ttl=64 time=0.713 ms
64 bytes from 100.92.87.31: icmp_seq=2 ttl=64 time=0.511 ms
64 bytes from 100.92.87.31: icmp_seq=3 ttl=64 time=0.578 ms
64 bytes from 100.92.87.31: icmp_seq=4 ttl=64 time=0.571 ms
64 bytes from 100.92.87.31: icmp_seq=5 ttl=64 time=0.584 ms
64 bytes from 100.92.87.31: icmp_seq=6 ttl=64 time=0.543 ms
64 bytes from 100.92.87.31: icmp_seq=7 ttl=64 time=0.654 ms
64 bytes from 100.92.87.31: icmp_seq=8 ttl=64 time=0.592 ms
64 bytes from 100.92.87.31: icmp_seq=9 ttl=64 time=0.575 ms
--- 100.92.87.31 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8180ms
rtt min/avg/max/mdev = 0.511/0.591/0.713/0.056 ms
```

## 4. Test 4 : We will ping two servers in the same data center in Johannesburg
```
# Zerotier connection to Server Johannesburg : Average : 0.105 mS
nickm@dell-laptop:~$ ping 10.244.77.51
PING 10.244.77.51 (10.244.77.51) 56(84) bytes of data.
64 bytes from 10.244.77.51: icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from 10.244.77.51: icmp_seq=2 ttl=64 time=0.122 ms
64 bytes from 10.244.77.51: icmp_seq=3 ttl=64 time=0.123 ms
64 bytes from 10.244.77.51: icmp_seq=4 ttl=64 time=0.125 ms
64 bytes from 10.244.77.51: icmp_seq=5 ttl=64 time=0.106 ms
64 bytes from 10.244.77.51: icmp_seq=6 ttl=64 time=0.105 ms
64 bytes from 10.244.77.51: icmp_seq=7 ttl=64 time=0.110 ms
64 bytes from 10.244.77.51: icmp_seq=8 ttl=64 time=0.108 ms
--- 10.244.77.51 ping statistics ---
8 packets transmitted, 8 received, 0% packet loss, time 7146ms
rtt min/avg/max/mdev = 0.044/0.105/0.125/0.024 ms

# Netbird connection to  Server Johannesburg Average : 182.099 mS
nickm@dell-laptop:~$ ping 100.103.13.160
PING 100.103.13.160 (100.103.13.160) 56(84) bytes of data.
64 bytes from 100.103.13.160: icmp_seq=1 ttl=64 time=205 ms
64 bytes from 100.103.13.160: icmp_seq=2 ttl=64 time=178 ms
64 bytes from 100.103.13.160: icmp_seq=3 ttl=64 time=176 ms
64 bytes from 100.103.13.160: icmp_seq=4 ttl=64 time=186 ms
64 bytes from 100.103.13.160: icmp_seq=5 ttl=64 time=177 ms
64 bytes from 100.103.13.160: icmp_seq=6 ttl=64 time=185 ms
64 bytes from 100.103.13.160: icmp_seq=7 ttl=64 time=176 ms
64 bytes from 100.103.13.160: icmp_seq=8 ttl=64 time=180 ms
64 bytes from 100.103.13.160: icmp_seq=9 ttl=64 time=177 ms
--- 100.103.13.160 ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8012ms
rtt min/avg/max/mdev = 175.826/182.099/204.529/8.618 ms

```
 
## Conclusion
Due to the way Zerotier has been designed and built it create a direct tunnel between connected machines.
We have proved that as local servers connect with similar speed directly or via Zerotier made connection

With Tailscale all traffic goes through the Tailscale server we see that in our results
However if you use Headscale you could inprove that

We did find that Netbird and Tailscale gave very similar results.

## Recomendations
If your servers are in the same country then all of these ZeroTrust networks gave similar results
If however you had servers in different continents then ZeriTier will make a big difference in performance
We found running an Oracle  Database in a different continent you need to use ZeroTier
However if you are an Oracle  Database in the same country all ZeroTrust networks gave similar performance

We were not able to Install Tailscale , Netbird ,  and ZeroTier on the same server
But we were able to make tests and get a conclustion
