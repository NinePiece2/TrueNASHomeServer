# TrueNASHomeServer

First off most of this project will be documented in this README markdown along with the history of the project, and much of the troubleshooting done to get the server to where it is now.

## Past Configurations and Insperations

To begin with this project started for me in 2017 when I first heard about FreeNAS through learning how to build computers on YouTube. Like most who are uneducated about the server space I thought it was an extremely complicated mechanism that would be extremely difficult to understand. Now I know that a server is just a regular computer with different hardware requirements running a different Operating System. So began my first FreeMAS server on an old laptop with a single 160 GB Hard Drive and a bootable USB. This system worked for what it was but was limited by the single 100 mbps ethernet port on the old laptop and was only used for archival data. 

It would eventually be upgraded to a 1TB Hard Drive and a 250 GB Boot SSD that would allow for more features to be used in newly released TrueNAS Core such as a plex media server. However, without any hardware redundancy and a cheap drive this NAS' storage drive would eventually become corrupted and all the data on it lost.

## Current Configurations

With the loss of data in mind as well as possible virtualization it was important to start with system that would not be easily corrupted. With the loss of data in mind as well as possible virtualization it was important to start with system that would not be easily corrupted. This means some sort of hardware redundancy, some of the options I considered were Raid 1, Raid Z and Raid Z2. Eventually I settled on using 3 4 Tb drives in Raid Z1 which would allow for one drive to fail completely without any data loss. 

When it comes to the other hardware I just tried to reuse as much previous hardware as I could and the current hardware configuration is shown in the image below: 
[<img src=images/CurrentConfig.png height=500>](images/CurrentConfig.png)
