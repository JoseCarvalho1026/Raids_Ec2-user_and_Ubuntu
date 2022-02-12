# **In AWS**

🔴 1 Instance (Ec2-user or Ubuntu);

🔴 1 Elastic IP (for each instance);

🔴 4 volumes (volumes of 1 GB and associate in instance).

# **In Termius**

## Raid 6

◻️ For `gdisk /dev/xvdf` , `gdisk /dev/xvdg`, `gdisk /dev/xvdh` and `gdisk /dev/xvdi` .
```
o Enter for new empty GUID partition table (GPT) ;
y Enter to confirm your decision ;
n Enter ;
Enter ;
Enter ;
Enter ;
fd00 and Enter ;
w Enter to write changes ;
y Enter to confirm your decision .
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
### Block required to use LVM

◻️ `pvcreate /dev/md0` ;

◻️ `pvscan` show the physical volumes ;

◻️ `pvdisplay` show detailed information of physical volumes ;

◻️ `vgcreate vg0 /dev/md0` create volume group ;

◻️ `vgdisplay` show volume group information ;

◻️ `lvcreate -n vg0lv0 -l +50%FREE vg0` create vg0lv0 with 1G ;

◻️ `lvcreate -n vg0lv1 -l +10%FREE vg0` create vg0lv1 with 1G ;

◻️ `lvdisplay` show logical volume information .
________________________________________________________
◻️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/vg0/vg0lv0` This will override data on /dev/md0 irrevocably. --- YES ;

◻️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/vg0/vg0lv1` This will override data on /dev/md0 irrevocably. --- YES ;

◻️ `cryptsetup luksOpen /dev/vg0/vg0lv0 vg0lv0_crypt` vg0lv0_crypt can be whatever name we want ;

◻️ `cryptsetup luksOpen /dev/vg0/vg0lv1 vg0lv1_crypt` vg0lv1_crypt can be whatever name we want ;

◻️ `mkfs.xfs /dev/mapper/vg0lv0_crypt` format filesystem ;

◻️ `mkfs.xfs /dev/mapper/vg0lv1_crypt` format filesystem .
________________________________________________________
### Creating folders

◻️ `cd /mnt` ;

◻️ `mkdir user` ;

◻️ `mkdir info` ;

◻️ `mount /dev/mapper/vg0lv0_crypt /mnt/user/` ;

◻️ `mount /dev/mapper/vg0lv1_crypt /mnt/info/` ;

◻️ `cat /etc/mtab` copy the two last lines ;

◻️ `nano /etc/fstab` paste the two lines and add "nofail," or ",nofail" .
________________________________________________________
◻️ `reboot` ;

◻️ `cryptsetup luksOpen /dev/vg0/vg0lv0 vg0lv0_crypt` open vg0lv0_crypt ;

◻️ `cryptsetup luksOpen /dev/vg0/vg0lv0 vg0lv1_crypt` open vg0lv1_crypt ;

◻️ `mount -a` assemble the units ;

◻️ `df -hT` see if the `mount -a` command worked ;

◻️ `cryptsetup status /dev/mapper/vg0lv0_crypt` ;

◻️ `cryptsetup status /dev/mapper/vg0lv1_crypt` ;

◻️ `cryptsetup luksDump /dev/mapper/vg0-vg0lv0` ;

◻️ `cryptsetup luksDump /dev/mapper/vg0-vg0lv1` .
