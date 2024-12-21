# Sniffing and Spoofing
## Task 1.1

The objective of this task is to learn how to use Scapy to do packet sniffing in Python programs. We were given this sample code:

```python
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface=’br-c93733e9f913’, filter=’icmp’, prn=print_pkt)
```

To get the name of the network interface in the VM we did the following:
<br> ![image1](docs/images/lb13_1.jpg) <br>
Here is the interface name: br-83f28a549e42, where 83f28a549e42 is the id of the network created with the ip 10.9.0.1. We changed the sample code to the correct interface name.

### Task 1.1 A

We ran the script on the attacker with root previleges.
```
$ sudo python3 sniffer.py
```
```python
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-83f28a549e42', filter='icmp', prn=print_pkt)
```

We then started pinging host B from host A:
<br> ![image2](docs/images/lb13_2.jpg) <br>
As we can see, the sniffing program was able to capture the packets:
<br> ![image3](docs/images/lb13_3.jpg) <br>
Here we can see the different layers: The Ethernet layer showed the source and destination MAC addresses and the packet type as IPv4. The IP layer included the source IP 10.9.0.6 and destination IP 10.9.0.5, along with TTL and the ICMP protocol. The ICMP layer showed an Echo Request (ping) with code 0. The raw data displayed the payload in hexadecimal format.

If we ran the program without root previleges, we would get an error:
<br> ![image4](docs/images/lb13_4.jpg) <br>
This happens because only root can access raw sockets, which are necessary for packet sniffing.

### Task 1.1 B

In this task we will be using filters in sniffing.
To capture only ICMP packets, we can use the code we were using in the previous task:
```python
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-83f28a549e42', filter='icmp', prn=print_pkt)
```
To capture any TCP packet that comes from a particular IP and with a destination port number 23, we use this code:
```python
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-83f28a549e42', filter='tcp and src host <SOURCE_IP> and dst port 23', prn=print_pkt)
```
To capture packets comes from or to go to a particular subnet. You can pick any subnet, such as 128.230.0.0/16; you should not pick the subnet that your VM is attached to:
```python
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-83f28a549e42', filter='net 128.230.0.0/16', prn=print_pkt)
```

