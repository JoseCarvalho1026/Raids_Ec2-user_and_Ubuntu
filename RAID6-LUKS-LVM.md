# **In AWS**

🔴 1 Instance (Ec2-user or Ubuntu);

🔴 1 Elastic IP (for each instance).

🔴 4 volumes (volumes of 1 GB and associate in instance)

# **In Termius**

## Raid 6

◻️ For `gdisk /dev/xvdf` , `gdisk /dev/xvdg` , `gdisk /dev/xvdh` and `gdisk /dev/xvdi`
```
o Enter for new empty GUID partition table (GPT) ;
y Enter to confirm your decision ;
n Enter ;
Enter ;
Enter ;
Enter ;
fd00 and Enter ;
w Enter to write changes ;
y Enter to confirm your decision ;
```
________________________________________________________
◻️ `mdadm --create /dev/md0 --level 6 --raid-devices 4 /dev/xvdf1 /dev/xvdg1 /dev/xvdh1 /dev/xvdi1` ;

◻️ `mdadm --detail /dev/md0` .
```
[---wait until you say---]
  State : clean
  Active Devices : 4
  Working Devices : 4
  Failed Devices : 0
  Spare Devices : 0
```
________________________________________________________
◻️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/md0` This will override data on /dev/md0 irrevocably. --- YES

◻️ `cryptsetup luksOpen /dev/md0 md0_crypt` md0_crypt can be whatever name we want ;

◻️ `cryptsetup luksDump -v /dev/md0` contains a lot of information ;

◻️ `ls -l /dev/mapper` shows the shortcut that was created .
________________________________________________________
### Block required to use LVM

◻️ `pvcreate /dev/mapper/md0_crypt` ;

◻️ `pvscan` show the physical volumes ;

◻️ `pvdisplay` show detailed information of physical volumes ;

◻️ `vgcreate vg0 /dev/mapper/md0_crypt` create volume group ;

◻️ `vgdisplay` show volume group information ;

◻️ `lvcreate -n lv0 -l +75%FREE vg0` create lv0 with 1.5G ;

◻️ `lvcreate -n lv1 -l +100%FREE vg0` create lv1 with 500 Mb ;

◻️ `lvdisplay` show logical volume information .
________________________________________________________
◻️ `mkfs.xfs /dev/vg0/lv0` format filesystem ;

◻️ `mkfs.ext4 /dev/vg0/lv1` format filesystem .
________________________________________________________
### Creating folders

◻️ `cd /mnt` ;

◻️ `mkdir user` ;

◻️ `mkdir info` .
________________________________________________________
◻️ `mount /dev/vg0/lv0 /mnt/user/` ;

◻️ `mount /dev/vg0/lv1 /mnt/info/` ;

◻️ `cat /etc/mtab` copy the last two lines ;

◻️ `nano /etc/fstab` paste the two lines and add "nofail," or ",nofail" .
________________________________________________________

◻️ `reboot` ;

◻️ `cryptsetup luksOpen /dev/md0 md0_crypt` serve para abrir `md0_crypt` ;

◻️ `mount -a` assemble the units ;

◻️ `df -hT` see if the mount -a command worked .
