

cat > lvm.yaml

apiVersion: lvm.topolvm.io/v1alpha1
kind: LVMCluster
metadata:
  name: example-lvmcluster
  namespace: openshift-storage
spec:
  storage:
    deviceClasses:
      - name: lvmvg
        thinPoolConfig:
          name: thinpool
          sizePercent: 90
          overprovisionRatio: 2
        devices:
          - /dev/sdb



[root@bootstrap abi]# oc get sc
NAME                   PROVISIONER   RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
lvms-lvmvg (default)   topolvm.io    Delete          WaitForFirstConsumer   true                   21m
[root@bootstrap abi]# 


[root@hub01 core]# vgs
  VG    #PV #LV #SN Attr   VSize    VFree 
  lvmvg   1   1   0 wz--n- <300.00g 30.00g
[root@hub01 core]# lvm
lvm> exit
  Exiting.
[root@hub01 core]# lvs
  LV       VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  thinpool lvmvg twi-a-tz-- 269.73g             0.00   10.42                           
[root@hub01 core]# lsblk 
NAME                   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sr0                     11:0    1   1.1G  0 rom  
vda                    252:0    0   200G  0 disk 
├─vda1                 252:1    0     1M  0 part 
├─vda2                 252:2    0   127M  0 part 
├─vda3                 252:3    0   384M  0 part /boot
└─vda4                 252:4    0 199.5G  0 part /var/lib/kubelet/pods/e197c977-c3bd-4703-b410-d240dc85ba6c/volume-subpaths/nginx-conf/monitoring-plugin/1
                                                 /var
                                                 /sysroot/ostree/deploy/rhcos/var
                                                 /usr
                                                 /etc
                                                 /
                                                 /sysroot
vdb                    252:16   0   300G  0 disk 
├─lvmvg-thinpool_tmeta 253:0    0   136M  0 lvm  
│ └─lvmvg-thinpool     253:2    0 269.7G  0 lvm  
└─lvmvg-thinpool_tdata 253:1    0 269.7G  0 lvm  
  └─lvmvg-thinpool     253:2    0 269.7G  0 lvm  
[root@hub01 core]# 






ZTP: 

mkdir -p ~/5g-deployment-lab/
mkdir -p clusters/{site-group-1,site-group-1/secrets/sno2,site-group-1/secrets/sno3,site-group-1/sno2-extra-manifest,site-group-1/sno3-extra-manifest}
mkdir -p policies/{site-specific-policies,resources,configuration-version-2024-03-04,configuration-version-2024-03-04/source-crs,configuration-version-2024-03-04/manifests}
touch clusters/{site-group-1,site-group-1/secrets/sno2,site-group-1/secrets/sno3}/.gitkeep
touch policies/{site-specific-policies,resources,configuration-version-2024-03-04,configuration-version-2024-03-04/source-crs,configuration-version-2024-03-04/manifests}/.gitkeep