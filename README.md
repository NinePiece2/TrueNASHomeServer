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

The software setup is quite simple, the server runs bare metal TrueNAS Scale Bluefin and using the built in hypervisor there are two virtual machines. As a side note the virtual machines use a bridged network interface so they can still communicate with the main TrueNAS server to get data from its SMB shares. The first is an ubuntu server VM that currently hosts a Minecraft and Assetto Corsa Servers and the second is a Proxmox VM that currently only hosts a Kemp Load Balancer on the Proxmox hypervisor. The load balancer lets me setup a DNS for all of the pages so that I can access them from anywhere around the world and they all have SSL certificates. The Kemp Load Balancer setup came from [here](https://www.youtube.com/watch?v=LlbTSfc4biw&ab_channel=NetworkChuck) however some modifications were needed as I was using Proxmox and not ESXI. All that was needed was a conversion from vmdk to qcow2 which can be done using the following code:

```
qemu-img convert -f vmdk filename.vmdk -O qcow2 filename.qcow2
```

As a note because Proxmox was not running on bare metal KVM hardware virtualization needed to be turned off:

[<img src=images/KVM.PNG height=400>](images/KVM.PNG)

After this the system would boot loop after reaching the login screen. This was fixed by changing the SCSI Controller to VMware PVSCSI in the hardware tab:

[<img src=images/SCSIController.PNG height=400>](images/SCSIController.PNG)

After this its as simple as following the rest of the YouTube video and setting everything up through Cloudflare. The load balancer is currently hosts the TrueNAS server on the domain [truenas.ninepiece2.tk]( truenas.ninepiece2.tk).


## Setting Up a Pair of Mellanox Connectx-3

This honestly should have been simpler but took a lot of time to figure out as there are not many people running this configuration. I bought 2 Mellanox ConnectX-3 CX354A Dual 40GbE QSFP cards that had been flashed to MCX354A-FCBT which allowed it to function in Ethernet mode as well as InfiniBand mode.

### Windows Setup

The setup on Windows 10 was very simple after getting the divers installed from [here](https://network.nvidia.com/products/adapter-software/ethernet/windows/winof-2/). Then going to device manager -> System Devices -> Mellanox ConnectX-3 VPI Network Adapter -> Properties -> Port Protocol and setting both ports to ETH for ethernet mode:

[<img src=images/MellanoxWindows.PNG height=400>](images/MellanoxWindows.PNG)

In the image both settings are grayed out as I set it up using Mellanox MFT which was unnecessary and done as a troubleshooting step. However, if you go to the options and see the option is grayed out download MFT from [here]( https://network.nvidia.com/products/adapter-software/firmware-tools/) and use [this]( https://blog.insanegenius.com/2020/06/21/moving-from-unraid-to-proxmox-ve/) guide to help you set it to Ethernet mode using MFT.

### TrueNAS Scale Setup

For this Operating System the setup was not as straight forward there are 2 main problems compared to the Windows setup. The first is that there is no way to change the ports on the card to Ethernet mode using a GUI so MFT needs to be used and the second problem is that the Kernal does not allocate memory for the driver by default.


## Modifications

After the initial setup another 3 drive Vdev was added and striped to the already exisiting Raid-Z1 pool.
