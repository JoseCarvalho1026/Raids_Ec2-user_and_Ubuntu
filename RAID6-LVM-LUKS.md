# **In AWS**

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

◻️ `pvscan` shows the physical volumes ;

◻️ `pvdisplay` shows detailed information of physical volumes ;

◻️ `vgcreate vg0 /dev/md0` creates volume group ;

◻️ `vgdisplay` shows volume group information ;

◻️ `lvcreate -n vg0lv0 -l +50%FREE vg0` creates vg0lv0 with 1G ;

◻️ `lvcreate -n vg0lv1 -l +10%FREE vg0` creates vg0lv1 with 1G ;

◻️ `lvdisplay` shows logical volume information .
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

◻️ `cat /etc/mtab` copies the two last lines ;

◻️ `nano /etc/fstab` pastes the two lines and add "nofail," or ",nofail" .
________________________________________________________
◻️ `reboot` ;

◻️ `cryptsetup luksOpen /dev/vg0/vg0lv0 vg0lv0_crypt` opens vg0lv0_crypt ;

◻️ `cryptsetup luksOpen /dev/vg0/vg0lv0 vg0lv1_crypt` opens vg0lv1_crypt ;

◻️ `mount -a` assembles the units ;

◻️ `df -hT` see if the `mount -a` commands worked ;

◻️ `cryptsetup status /dev/mapper/vg0lv0_crypt` ;

◻️ `cryptsetup status /dev/mapper/vg0lv1_crypt` ;

◻️ `cryptsetup luksDump /dev/mapper/vg0-vg0lv0` ;

◻️ `cryptsetup luksDump /dev/mapper/vg0-vg0lv1` .
