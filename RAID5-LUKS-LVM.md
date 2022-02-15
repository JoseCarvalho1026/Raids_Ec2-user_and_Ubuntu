# **In AWS**

🔴 1 Instance (Ec2-user or Ubuntu);

🔴 1 Elastic IP (for each instance);

🔴 3 volumes (volumes of 1 GB and associate in instance).

# **In Termius**

## Raid 5

◻️ For `gdisk /dev/xvdf` , `gdisk /dev/xvdg` and `gdisk /dev/xvdh` .
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
◻️ `mdadm --create /dev/md0 --level 5 --raid-devices 3 /dev/xvdf1 /dev/xvdg1 /dev/xvdh1` ;

◻️ `mdadm --detail /dev/md0` .
```
[---wait until you say---]
  State : clean
  Active Devices : 3
  Working Devices : 3
  Failed Devices : 0
  Spare Devices : 0
```
________________________________________________________
◻️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/md0` This will override data on /dev/md0 irrevocably. --- YES ;

◻️ `cryptsetup luksOpen /dev/md0 md0_crypt` md0_crypt can be whatever name we want ;

◻️ `cryptsetup luksDump -v /dev/md0` contains a lot of information ;

◻️ `ls -l /dev/mapper` shows the shortcut that was created .
________________________________________________________
### Block required to use LVM

◻️ `pvcreate /dev/mapper/md0_crypt` ;

◻️ `pvscan` shows the physical volumes ;

◻️ `pvdisplay` shows detailed information of physical volumes ;

◻️ `vgcreate vg0 /dev/mapper/md0_crypt` creates volume group ;

◻️ `vgdisplay` shows volume group information ;

◻️ `lvcreate -n lv0 -l +100%FREE vg0` creates lv0 with 2G ;

◻️ `lvdisplay` shows logical volume information ;

◻️ `mkfs.xfs /dev/vg0/lv0` formats filesystem .
________________________________________________________
### Creating folders

◻️ `cd /mnt` ;

◻️ `mkdir user` ;

◻️ `mount /dev/vg0/lv0 /mnt/user/` ;

◻️ `cat /etc/mtab` copy the last line ;

◻️ `nano /etc/fstab` pastes the line and add "nofail," or ",nofail" .
________________________________________________________
◻️ `reboot` ;

◻️ `cryptsetup luksOpen /dev/md0 md0_crypt` opens `md0_crypt` ;

◻️ `mount -a` assemblses the units ;

◻️ `df -hT` sees if the mount -a command worked ;

◻️ `cryptsetup status /dev/mapper/md0_crypt` ;

◻️ `cryptsetup luksDump /dev/md0` .
