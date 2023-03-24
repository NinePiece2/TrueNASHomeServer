# TrueNASHomeServer

First off most of this project will be documented in this README markdown along with the history of the project, and much of the troubleshooting done to get the server to where it is now.

## Past Configurations and Insperations

To begin with this project started for me in 2017 when I first heard about FreeNAS through learning how to build computers on YouTube. Like most who are uneducated about the server space I thought it was an extremely complicated mechanism that would be extremely difficult to understand. Now I know that a server is just a regular computer with different hardware requirements running a different Operating System. So began my first FreeMAS server on an old laptop with a single 160 GB Hard Drive and a bootable USB. This system worked for what it was but was limited by the single 100 mbps ethernet port on the old laptop and was only used for archival data. 

It would eventually be upgraded to a 1TB Hard Drive and a 250 GB Boot SSD that would allow for more features to be used in newly released TrueNAS Core such as a plex media server. However, without any hardware redundancy and a cheap drive this NAS' storage drive would eventually become corrupted and all the data on it lost.

## Current Configurations

With the loss of data in mind as well as possible virtualization it was important to start with system that would not be easily corrupted. With the loss of data in mind as well as possible virtualization it was important to start with system that would not be easily corrupted. This means some sort of hardware redundancy, some of the options I considered were Raid 1, Raid Z and Raid Z2. Eventually I settled on using 3 4 Tb drives in Raid Z1 which would allow for one drive to fail completely without any data loss. 

When it comes to the other hardware I just tried to reuse as much previous hardware as I could and the current hardware configuration is shown in the image below: 

<br/>

[<img src=images/CurrentConfig.PNG height=500>](images/CurrentConfig.PNG)

## Virtual Environments 

The software setup is quite simple, the server runs bare metal TrueNAS Scale Bluefin and using the built in hypervisor there are two virtual machines. The first is an ubuntu server VM that currently hosts a Minecraft and Assetto Corsa Servers and the second is a Proxmox VM that currently only hosts a Kemp Load Balancer on the Proxmox hypervisor. The load balancer lets me setup a DNS for all of the pages so that I can access them from anywhere around the world and they all have SSL certificates. The Kemp Load Balancer setup came from [here](https://www.youtube.com/watch?v=LlbTSfc4biw&ab_channel=NetworkChuck) however some modifications were needed as I was using Proxmox and not ESXI. All that was needed was a conversion from vmdk to qcow2 which can be done using the following code: 

```
qemu-img convert -f vmdk filename.vmdk -O qcow2 filename.qcow2
```

As a note because Proxmox was not running on bare metal KVM hardware virtualization needed to be turned off:

[<img src=images/KVM.PNG height=400>](images/KVM.PNG)

After this the system would boot loop after reaching the login screen. This was fixed by changing the SCSI Controller to VMware PVSCSI in the hardware tab:

[<img src=images/SCSIController.PNG height=400>](images/SCSIController.PNG)

## Modifications

After the initial setup another 3 drive Vdev was added and striped to the already exisiting Raid-Z1 pool. 
