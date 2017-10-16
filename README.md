# Open_vSwitch_TheEasyWay  
tried and tried to configure open vSwitch in my home lab for over ten times, I assume I get to an easy way to set up open vSwitch, at least working, with the most easy way, which is especially useful for the first time users like me.  

## The way I think ovs works  
per my dummy understanding, open vSwitch works like:  
a. create a virtual switch (often named br0 because we know bridge ~= switch);  
b. add "real"/physic NIC port to br0;  
c. remove IP address from physic NIC port, then move the IP to br0 (the vritual switch);  
d. bring br0 up.  
I think that's enough basic understanding for first time users.  

this video introduces OVS very good.  
https://www.youtube.com/watch?v=rYW7kQRyUvA&t=275s  

## My Enviroment  
Ubuntu Server 16.04.3 LTS installed on VMWare Player.
The network interface name is 'ens33', a "Predictable Network Interface Name".  
Retrieving IP address via DHCP.  
```
erich@ubuntu:~$ ifconfig
```
          ens33     Link encap:Ethernet  HWaddr 00:0c:29:a3:5b:47
                    inet addr:192.168.245.142  Bcast:192.168.245.255  Mask:255.255.255.0
                    inet6 addr: fe80::20c:29ff:fea3:5b47/64 Scope:Link
                    UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                    RX packets:146 errors:0 dropped:0 overruns:0 frame:0
                    TX packets:85 errors:0 dropped:0 overruns:0 carrier:0
                    collisions:0 txqueuelen:1000
                    RX bytes:16416 (16.4 KB)  TX bytes:19258 (19.2 KB)

## Step by Step 

### Install open vSwitch
```
sudo apt update
sudo apt install -y openvswitch-switch
sudo ovs-vsctl --version
```
    ovs-vsctl (Open vSwitch) 2.5.2
    ...
    DB Schema 7.12.1

### Create a Virtual Switch  
I name it "myVirtualSwitch" instead of often used 'br0', you can name it anything you like. 
```
sudo ovs-vsctl add-br myVirutalSwitch
sudo ifconfig myVirtualSwitch up

sudo ovs-vsctl show
```
    e4eebcc7-b9a7-4d42-bc71-972a0ebe1cc5
        Bridge myVirtualSwitch
            Port myVirtualSwitch
                Interface myVirtualSwitch
                    type: internal
        ovs_version: "2.5.2"

Note: With next step, you will lose connection to the host if you SSH to it.  You'd better sit in front of your computer. 
Or you shall run these commands in one line.  
```
sudo ovs-vsctl add-port myVirtualSwitch ens33 && sudo ifconfig ens33 0 && sudo dhclient myVirutalSwitch
```
### Connect ens33 (my "real" network interface) to myVirtualSwitch
   
```
sudo ovs-vsctl add-port myVirtualSwitch ens33

```
### Remove IP addressing on the "real" ens33; and enable DHCP on myVirtualSwitch
```
sudo ifconfig ens33 0
sudo dhclient myVirutalSwitch

ifconfig
```
          ens33     Link encap:Ethernet  HWaddr 00:0c:29:a3:5b:47
                    inet6 addr: fe80::20c:29ff:fea3:5b47/64 Scope:Link
                    UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                    RX packets:3352 errors:0 dropped:0 overruns:0 frame:0
                    TX packets:1442 errors:0 dropped:0 overruns:0 carrier:0
                    collisions:0 txqueuelen:1000
                    RX bytes:2067580 (2.0 MB)  TX bytes:129613 (129.6 KB)

          myVirtualSwitch Link encap:Ethernet  HWaddr 00:0c:29:a3:5b:47
                    inet addr:192.168.245.142  Bcast:192.168.245.255  Mask:255.255.255.0
                    inet6 addr: fe80::20c:29ff:fea3:5b47/64 Scope:Link
                    UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                    RX packets:490 errors:0 dropped:135 overruns:0 frame:0
                    TX packets:74 errors:0 dropped:0 overruns:0 carrier:0
                    collisions:0 txqueuelen:1
                    RX bytes:43361 (43.3 KB)  TX bytes:16946 (16.9 KB)


## One-Line Version
```
sudo apt update && \
sudo apt install -y openvswitch-switch && \
sudo ovs-vsctl add-br br0 && \
sudo ifconfig br0 up && \
sudo ovs-vsctl add-port br0 ens33 && \
sudo ifconfig ens33 0 && \
sudo dhclient myVirutalSwitch && \
ifconfig
```



