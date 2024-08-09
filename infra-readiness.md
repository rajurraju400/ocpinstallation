# This documentation help to create a Linux bridge on wired ethernet adaptor on your Linux server. 


### Installating bridge rpm

```
root@ocphypervisor:~# yum install bridge-utils  -y
Repository anydesk is listed more than once in the configuration
Last metadata expiration check: 0:48:27 ago on Fri 09 Aug 2024 07:15:43 AM CDT.
Package bridge-utils-1.7.1-9.fc40.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
root@ocphypervisor:~# 
```


### Create a bridge using nmcli 

my physical interface from OS level is `enp1s0`, so i created a bridge called br-public and mapped `enpls0` as master for it. 

```
nmcli con add ifname br-public type bridge con-name br-public
sudo nmcli connection add type ethernet ifname enp1s0 master br-public
nmcli con modify br-public ipv4.method static ipv4.address 192.168.0.129/24 ipv4.gateway "192.168.0.1" ipv4.dns 192.168.0.125 ipv4.dns-search arya.lab ipv6.method ignore
nmcli con up br-public
nmcli conn
```



```
root@ocphypervisor:~# nmcli con show 
NAME                 UUID                                  TYPE      DEVICE        
br-public            964f0519-d2f2-4db8-8609-fbc6ee29cf67  bridge    br-public     
eno1                 93d51564-a76c-44d3-9771-c339a284d334  ethernet  eno1          
bridge-slave-enp1s0  e74028f7-ff5a-4d0f-b9d8-445101eb3258  ethernet  enp1s0        
lo                   b146f11d-4e9d-4ca0-bc87-9e9e1f51e649  loopback  lo            
tnc                  dd6ef0d5-c72c-4349-bd28-397fed5bd404  bridge    tnc           
tnc-connected        a906893f-4a39-42d1-94a4-f3384e5bca3d  bridge    tnc-connected 
vnet5                f0825d90-6a9e-40cb-ae83-2c09769b99f5  tun       vnet5         
vnet6                bb8b3b9a-2d93-4980-aecb-20a7c367cfe5  tun       vnet6         
vnet7                e8daac92-a217-44b3-85b9-307a59c5111f  tun       vnet7         
root@ocphypervisor:~# 

```



```
root@ocphypervisor:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether d8:9d:67:f4:5b:45 brd ff:ff:ff:ff:ff:ff
    altname enp0s25
    inet 10.10.10.11/24 brd 10.10.10.255 scope global noprefixroute eno1
       valid_lft forever preferred_lft forever
    inet6 fe80::8012:7af9:e448:9db/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br-public state UP group default qlen 1000
    link/ether d8:9d:67:f4:5b:44 brd ff:ff:ff:ff:ff:ff
4: br-public: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether d8:9d:67:f4:5b:44 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.129/24 brd 192.168.0.255 scope global noprefixroute br-public
       valid_lft forever preferred_lft forever
    inet6 fd0c:a749:c7f:78ad:da9d:67ff:fef4:5b44/64 scope global dynamic mngtmpaddr proto kernel_ra 
       valid_lft 1676sec preferred_lft 1676sec
    inet6 fe80::da9d:67ff:fef4:5b44/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
5: tnc: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:52:62:3a brd ff:ff:ff:ff:ff:ff
    inet 192.168.125.1/24 brd 192.168.125.255 scope global tnc
       valid_lft forever preferred_lft forever
6: tnc-connected: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:9e:67:03 brd ff:ff:ff:ff:ff:ff
    inet 192.168.126.1/24 brd 192.168.126.255 scope global tnc-connected
       valid_lft forever preferred_lft forever
12: vnet5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-public state UNKNOWN group default qlen 1000
    link/ether fe:54:00:1e:bc:ad brd ff:ff:ff:ff:ff:ff
    inet6 fe80::fc54:ff:fe1e:bcad/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
13: vnet6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-public state UNKNOWN group default qlen 1000
    link/ether fe:54:00:70:cb:86 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::fc54:ff:fe70:cb86/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
14: vnet7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-public state UNKNOWN group default qlen 1000
    link/ether fe:54:00:7e:e7:a3 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::fc54:ff:fe7e:e7a3/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
root@ocphypervisor:~# 

```