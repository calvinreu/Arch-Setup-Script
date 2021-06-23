### Introduction
This is my fork of [easy-arch](https://github.com/classy-giraffe/easy-arch), a **script** made in order to boostrap a basic **Arch Linux** environment with **snapshots** and **encryption** by using a fully automated process.

This fork comes with various security improvements and fully working rollbacks with snapper. I do submit some of the changes here back to upstream as well.

### How does it work?
1. Download an Arch Linux ISO from [here](https://archlinux.org/download/)
2. Flash the ISO onto an [USB Flash Drive](https://wiki.archlinux.org/index.php/USB_flash_installation_medium).
3. Boot the live environment.
4. Connect to the internet.
5. `git clone https://github.com/tommytran732/Arch-Setup-Script/`
6. `cd Arch-Setup-Script`
7. `chmod u+x ./install.sh && ./install.sh`
8. do `arch-chroot /mnt` and create your wheel user once the script is done. Remember to give the wheel group priviledges in `visudo`.

### Snapper behavior
The partition layout I use rallows us to replicate the behavior found in openSUSE 🦎
1. Snapper rollback <number> works! You will no longer need to manually rollback from a live USB like you would with the @ and @home layout suggested in the Arch Wiki.f2fs-tools udftools
2. You can boot into a readonly snapshot! GDM and other services will start normally so you can get in and verify that everything works before rolling back.
3. Automatic snapshots on pacman install/update operations
4. /boot and /boot/efi are 2 seperate subvolumes which will not be rolled back with snapper.
5. For consistency with pacman's database, I deviate from SUSE's partition layout leave /usr/local/ and /opt as part of the snapshot. When you rollback, everything in those 2 directories rollback as well.
6. GRUB will boot into the default BTRFS snapshot set by snapper. Like on SUSE, your running system will always be a read-write snapshot in @/.snapshots/X/snapshot. 

### Changes to the original project
1. Encrypted /boot
2. SUSE - like partition layout
3. Snapper snapshots & rollback
4. Default umask to 077
5. Firewalld is enabled by default
6. Minimally setup GNOME 40 with pipewire
7. Added more filesystem support (Since Disk Utility is a GNOME dependency and it supports exFAT, NTFS, F2FS and UDF, I added support for those out of the box to make the experience a bit better out of the box)
8. Randomize Mac Address and disable Connectivity Check for privacy
9. Blacklisted Firewire SBP2 (As recommended by https://www.ncsc.gov.uk/collection/end-user-device-security/platform-specific-guidance/ubuntu-18-04-lts)
10. Kernel security settings

### Partitions layout 

| Partition/Subvolume | Label                        | Mountpoint               | Notes                       |
|---------------------|------------------------------|--------------------------|-----------------------------|
| 1                   | ESP                          | /boot/efi                | Unencrypted FAT32           |
| 2                   | @/.snapshots/X/snapshot      | /                        | Encrypted BTRFS             |
| 3                   | @/boot                       | /boot/                   | Encrypted BTRFS (nodatacow) |
| 4                   | @/root                       | /root                    | Encrypted BTRFS             |
| 5                   | @/home                       | /home                    | Encrypted BTRFS             |
| 6                   | @/.snapshots                 | /.snapshots              | Encrypted BTRFS             |
| 7                   | @/srv                        | /srv                     | Encrypted BTRFS (nodatacow) |
| 8                   | @/tmp                        | /tmp                     | Encrypted BTRFS (nodatacow) |
| 9                   | @/var_log                    | /var/log                 | Encrypted BTRFS (nodatacow) |
| 10                  | @/var_crash                  | /var/crash               | Encrypted BTRFS (nodatacow) |
| 11                  | @/var_cache                  | /var/cache               | Encrypted BTRFS (nodatacow) |
| 12                  | @/var_tmp                    | /var/tmp                 | Encrypted BTRFS (nodatacow) |
| 13                  | @/var_spool                  | /var/spool               | Encrypted BTRFS (nodatacow) |
| 14                  | @/var_lib_libvirt_images     | /var/lib/libvirt/images  | Encrypted BTRFS (nodatacow) |

### To do
1. Automate wheel user setup
2. Install yay and setup opensnitch
3. Reduce the number of password prompts
4. Automatic secure boot setup with your own keys (no, we are not using shim).
5. Optional Nvidia driver installation 
6. Automatic zram setup
