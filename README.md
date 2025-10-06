# RaspberryPi-Image-Backup
## Backup your Pi image on the fly via network share

**HOWTO save a Raspbian Image on a smb network share**  
Save a complete SD-Card with a Raspbian image to a samba-share during operation.
First find out the device name of the sd card. Log in the pi via ssh. Then:  

<code>
pi@Mauersegler:~ $ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0     179:0    0 14,9G  0 disk
├─mmcblk0p1 179:1    0 43,2M  0 part /boot
└─mmcblk0p2 179:2    0 14,8G  0 part /
</code>  
In this case mmcblk0 is the sd card.
Now establish a mount point on the raspberry pi:  

<code> pi@Mauersegler:/mnt $ sudo mkdir /mnt/Sicherung </code>
and check:  
<code> pi@Mauersegler:~  $ ls /mnt/ </code>
<p> </p>
<code> Sicherung </code>

Now make a folder on a samba share of the target. You need a folder on a target share which is accessible from the pi (e.g. a folder on a NAS).
You need the address of the share and access to it:
In my case:
<code>smb://192.168.1.13/public/RaspberryPi</code>

Now mount the network resource on the pi. First you have to create a file named <code> .smbcredentials </code> in the home directory of the current user (normally pi):  

<code> pi@Mauersegler:~ $ echo -e 'username=<USER> \n password=<PASSWORD> \n\r' > \~/.smbcredentials </code>  

Change <USER> to the name of your user and <PASSWORD> to the password of this user on the mounted smb share.  
(USER and PASSWORD are the credentials of the samba share, not an the raspberry pi)  
Change the access properties so that only you can read and modify these:  

<code>pi@Mauersegler:~ $ chmod 600 ~/.smbcredentials</code>  

Now mount the share:

<code> pi@Mauersegler:~ $ sudo mount -t cifs -o credentials=/home/pi/.smbcredentials,uid=1000,gid=100 //192.168.1.13/public/RaspberryPi/ /mnt/Sicherung/ </code>

Now you can start the backup with:

<code>pi@Mauersegler:~ $ sudo dd if=/dev/mmcblk0 of=/mnt/Sicherung/<MYBACKUPIMAGE.img> </code>  

Copying the whole sd-card can take a a while ....  

To get the image in a smaller, easier manageable size you can shrink it with the pishrink.sh script. 
(Newer versions can be found here: https://github.com/Drewsif/PiShrink).  

Start the script with:
<code> pi@Mauersegler:~ $ sudo ./pishrink.sh <Path to Image>/<MYBACKUPIMAGE.img> <Path to shriked Image>/<MYBACKUPIMAGE>_pishrink.img </code>  

If you use the switch -s , the saved image expands the file system of the image during next boot of the maximum size of the sd card you use (File System Expansion).
