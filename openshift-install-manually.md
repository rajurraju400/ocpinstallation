# This document will help you to install Redhat Openshift manually

## infra information






## infra readiness

Login to your fedora workstation and setup the infra readiness tasks

```
root@hypervisor:~# mkdir -p ocp
root@hypervisor:~# kcli create pool -p /var/lib/libvirt/images default
root@hypervisor:~/ocp# wget https://dl.rockylinux.org/pub/rocky/9/images/x86_64/Rocky-9-GenericCloud-Base.latest.x86_64.qcow2
Rocky-9-GenericCloud 100% [======================================================================================================================================>]  576.00M    2.99MB/s
                          [Files: 1  Bytes: 576.00M [3.39MB/s] Redirects: 0  Todo: 0  Errors: 0                                                                   ]    1.97MB/s          
root@hypervisor:~/ocp#
root@hypervisor:~/ocp# mv Rocky-9-GenericCloud-Base.latest.x86_64.qcow2 /var/lib/libvirt/images/
root@hypervisor:~/ocp# cd /var/lib/libvirt/images
root@hypervisor:/var/lib/libvirt/images# chown qemu:qemu Rocky-9-GenericCloud-Base.latest.x86_64.qcow2
root@hypervisor:/var/lib/libvirt/images# 
```

## Creating Networking:

### Creating Virtual Network Bridges: 
The virual environment will comprise a virtual machine that will act as our jumphost server (or Bastion, as its commonly called), and one or more VMs that will be used to install the OpenShift Cluster. 

```
kcli create network -c 192.168.10.0/24 -P dhcp=false --domain ocp.bootcamp.lab ocp-nat
```


