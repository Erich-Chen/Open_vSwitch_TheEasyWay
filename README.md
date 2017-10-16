# Open_vSwitch_TheEasyWay  
tried and tried to configure open vSwitch in my home lab for over ten times, I assume I get to an easy way to set up open vSwitch, at least working, with the most easy way, which is especially useful for the first time users like me.  

## The way I think ovs works  
per my dummy understanding, open vSwitch works like:  
a. create a virtual switch (often named br0 because we know bridge ~= switch);  
b. add real NIC port to br0;  
c. remove IP address from physic NIC port, then move the IP to br0 (the vritual switch); 
d. bring br0 up.  
I think that's enough basic understanding for first time users.  

this video introduces OVS very good.  
https://www.youtube.com/watch?v=rYW7kQRyUvA&t=275s  

## My Enviroment  
Ubuntu Server 16.04.3 LTS installed on VMWare Player.
The network interface name is 'ens33', a "Predictable Network Interface Name".  
```
erich@ubuntu:~$ ifconfig
ens33     Link encap:Ethernet  HWaddr 00:0c:29:a3:5b:47
          inet addr:192.168.245.142  Bcast:192.168.245.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fea3:5b47/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:146 errors:0 dropped:0 overruns:0 frame:0
          TX packets:85 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:16416 (16.4 KB)  TX bytes:19258 (19.2 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:160 errors:0 dropped:0 overruns:0 frame:0
          TX packets:160 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:11840 (11.8 KB)  TX bytes:11840 (11.8 KB)

```

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
sudo ovs-vsctl show
```
    e4eebcc7-b9a7-4d42-bc71-972a0ebe1cc5
        Bridge myVirtualSwitch
            Port myVirtualSwitch
                Interface myVirtualSwitch
                    type: internal
        ovs_version: "2.5.2"

### 
