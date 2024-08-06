# Installing Openshift on a disconnected infra with single node setup

This lab guide uses a baremetal server to deomonstrate the deployment of Openshift in a fully disconnected infra using ABI (Agent based installer)

1) infra readiness
    * Workstation installation and it's configuration
    * DNS configuration
    * [Architecture Diagram](#architecture-diagram)
2) OCP installation
    * Installing the mirror-registry
    * Mirroring Openshift image and operators to the local image registry
    * Genering an ISO file and booting the OpenShift Node(s) with the ISO

## Workstation installation and configuration

I do have HP workstation z420(Very old bad guy)loaded with below hw configuration and it's enough to test OCP installation

```
Processors: 2x (3.00 GHz) 10-Core Intel Xeon E5-2690V2 Processors [multi threading enabled, so total 40 vcps]
Memory (RAM): 256GB (8x32GB) DDR3 PC3 R Memory
Drive 1: 3TB HDD SATA 7.2k 3.5" Hard Drive
Drive 2: 512GB SSD SATA 6GB/s 2.5" Solid State Drive 
Drive 3: 3TB HDD SATA 7.2k 3.5" Hard Drive
Drive 4: 3TB HDD SATA 7.2k 3.5" Hard Drive
Graphics Card: Nvidia Quadro K620 2GB DDR3 (DVI, DisplayPort)
Operating System: Fedora Workstation 40
```

### Disk and File system 

```
root@ocphypervisor:/home/raj# pvs
  PV         VG     Fmt  Attr PSize    PFree  
  /dev/sda   datavg lvm2 a--  <476.94g      0 
  /dev/sdb3  fedora lvm2 a--    <2.73t      0 
  /dev/sdc   datavg lvm2 a--    <2.73t 199.45g
  /dev/sdd   datavg lvm2 a--    <2.73t  <2.73t
root@ocphypervisor:/home/raj# vgs
  VG     #PV #LV #SN Attr   VSize  VFree
  datavg   3   1   0 wz--n-  5.92t 2.92t
  fedora   1   1   0 wz--n- <2.73t    0 
root@ocphypervisor:/home/raj# lvs
  LV              VG     Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  var-lib-libvirt datavg -wi-ao----  3.00t                                                    
  root            fedora -wi-ao---- <2.73t                                                    
root@ocphypervisor:/home/raj# df -hP
Filesystem                            Size  Used Avail Use% Mounted on
/dev/mapper/fedora-root               2.8T   63G  2.7T   3% /
devtmpfs                              4.0M     0  4.0M   0% /dev
tmpfs                                 126G  1.1M  126G   1% /dev/shm
efivarfs                              384K   77K  303K  21% /sys/firmware/efi/efivars
tmpfs                                  51G  2.8M   51G   1% /run
tmpfs                                 126G   16K  126G   1% /tmp
/dev/mapper/datavg-var--lib--libvirt  3.0T  229G  2.8T   8% /var/lib/libvirt
/dev/sdb2                             974M  368M  539M  41% /boot
/dev/sdb1                             599M   20M  580M   4% /boot/efi
tmpfs                                  26G  296K   26G   1% /run/user/1000
root@ocphypervisor:/home/raj#
```
## Architecture Diagram

![Architecture Diagram](/images/allinone.png)


