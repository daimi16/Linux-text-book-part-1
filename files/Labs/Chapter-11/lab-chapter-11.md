# Lab - Chapter 11

## Lab 11 Objectives

* Creating virtual disks in VirtualBox
* Creating new partitions in fdisk
* Creating new filesystems with mkfs
* Creating new filesystems in ZFS and Btrfs
* Mounting new filesystems
* Editing `/etc/fstab` and using systemd .mount files to make our mounts permanent

## Lab 11 Outcomes

At the conclusion of this lab you will have successfully created a new virtual disk in VirtualBox, created new partitions using fdisk, formatted those partitions using mkfs, XFS, and ZFS, and mounted all those partitions manually and automatically using the `/etc/fstab`.

## Lab 11 Activities


For each of the bullet points, take a screenshot of the output of the commands to display the content to demonstrate the concepts.  Note - make your screenshot efficient, and capture only relevant data along with numbering the output.  All disks that are created can be 2 GB unless noted.

1. Create 1 virtual drive in VirtualBox:

   a. Use fdisk to create a primary partition
   b. Format it with ext4
   c. Mount it to /mnt/disk1
   d. Add it to your fstab

2. Create 2 more virtual drives:

   a. Create a single volume group named vg-group
   b. Create 1 logical volume named lv-group using the two drives
   c. Format it with XFS
   d. Mount it to /mnt/disk2
   e. Add the lv-group to your fstab, reboot the system and `cat` the  `/etc/fstab` and show that your entry is present.

3. Using the same LVM as before:

   a. Add an additional VirtualBox disk and the create a LVM physical disk
   b. Grow the volume group and logical volume
   c. Grow the XFS file system

4. Using LVM of the previous exercise on the logical volume lv-group

   a. Using either `fallocate` or `truncate` commands, create a file 25 megabytes in size and name it **datadump.txt** on your Logical Volume
   b. Reference Section 11.5.4 LVM Snapshots: create an LVM snapshot of the logical volume named `lv-backup`
   c. Mount the snapshot to `/mnt/disk3` (create this location if not existing)
   d. `ls -l` the contents of `/mnt/disk3`

5. Using Ubuntu 20.04 and ZFS, attach four 1 GB disks and create RAID 10 (a mirrored stripe). Display the `zpool status` and take a screenshot of the output.

6. Using Ubuntu 20.04, attach 4 virtual disks of 1 GB each. Create two Btrfs mirrored drives named disk1 and disk2.  Take a screenshot of the output of the `btrfs filesystem show` command for each disk.

7. From the previous question, attach another 1 GB virtual disk. Create a Btrfs partition on this disk named disk3.  Create a snapshot of disk1 save it to the newly created disk.  Use the `btrfs subvolume list` command to generate output for a snapshot. Reference [https://docs.oracle.com/cd/E37670_01/E37355/html/ol_use_case3_btrfs.html](https://docs.oracle.com/cd/E37670_01/E37355/html/ol_use_case3_btrfs.html "btrfs documentation for subvolumes")

8. Using Fedora, attach 4 1 GB disks in a Btrfs stripe.  

    a. Take a screenshot of the `btrfs filesystem df` command for this volume.  
    b. Then remove one of the virtual disks from the stripe.  Take a screenshot of the `btrfs filesystem df` command for this volume.
    c. Attach an additional 2 gb disk to the Btrfs stripe. Take a screenshot of the `btrfs filesystem df` command for this volume.  
    d. Extend the Btrfs filesystem to encompass using all of the new disk space. Take a screenshot of the `btrfs filesystem df` command for this volume.

9. From the previous exercise using your ZFS pool named datapool, create a 25 megabyte file named datadump.txt:

   a. Attach a third virtual disk to the system and create a zpool named backup
   b. Execute the `ls -lh` command to display the file and its size
   c. Take a ZFS snapshot of the datapool named @today - take a screenshot of the command output: `zfs list -s snapshot`
   d. Using the ZFS send and recv commands copy the @today snapshot to the zpool named backup
   e. Execute `ls -lh` command on the zpool backup
   f. Using the commandline, append an additional 25 mb to `/datapool/datadump.txt`
   g. Execute an `ls -lh` on zpool datapool and backup to compare the two files
   h. Take a screenshot of the command output: `zfs list -s snapshot` to show the growth in the snapshot

10. Create a systemd .mount unit file for the Btrfs partitions created in question number 8

    a. List the content of the .mount files in a screenshot(s)
    b. Reboot the system and take a screenshot of the df -H to see the .mount files work

11. You will need two systems with bridged networking and ssh enabled, create a 25 megabyte file named databasedump.txt on the zpool datapool:

    a. On the first system (the system without zpool datapool), create a datapool name backuppool (you might need to attach a virtual disk to do this)
    b. Take a snapshot of the zpool datapool and name it @now
    c. Execute the remote send and recv command over ssh to migrate the snapshot to the pool backuppool (You may need to exchange SSH keys via `ssh-keygen` and `ssh-copy-id` first to make this work)

12. On the zpool named datapool on Ubuntu:

    a. Execute a ```zpool status``` command
    b. Enable LZ4 compression on the zpool datapool
    c. Execute a `zfs get all | grep compression` command to display that compression is enabled

13. On the zpool named datapool, execute a `zpool status` command:

    a. Execute a scrub of the zpool datapool
    b. Create a cron job that executes a zfs scrub on the zpool datapool at 3 am every Sunday morning

14. Using the sample from the text on your Ubuntu 20.04 system, add two additional virtual disks:

    a. Create two partitions on each of these devices
    b. Then using the sample code add these two devices as a log and a cache to the zpool datapool
    c. Execute a `zpool status` command for the zpool named datapool

15. On your Fedora system execute, any of the commands listed to print out the disk serial numbers.

16. Using an Ubuntu system of your choice, create two pair of four 2-GB virtual disks.  Create a ZFS stripe on one of the four disk arrays and create a ZFS equivalent of a RAID 10 (striped mirror) on the other 4 disk array.  Run the command `sudo zpool status` and capture the output.  Name the first zpool, **zstripe** and the second zpool, **zmirror** -- make sure to `chown` to your user `/zstripe` and `/zmirror`

    a. Install the Nginx webserver.  We will modify the file `/etc/nginx/sites-enabled/default` and change the default root setting to serve from the directory `/zstripe`.

17. Attach an additional 2 GB virtual disk and format it with Btrfs and we will mount is in read-only mode. Using the command `lsblk --fs /dev/sdX` determine the UUID of the newest virtual disk you just created.  Add an entry for this disk to the `/etc/fstab` file with the following values:

    a. file system is UUID=
    b. mount point is `/mnt/disk100` (create this partition if it doesn't exist)
    c. type is btrfs
    d. options: defaults,ro  (ro for read-only)
    e. dump and pass fields can be 0
    f. Change owner and group to your username for `/mnt/disk100` (using `chmod`)
    g. Reboot your system. Change directory to `/mnt/disk100` and take a screenshot to demonstrate that the disk is in read-only mode by trying to create a file via this command:  `touch demo.txt`

18. Using an OS of your choice, create 4 2 GB Virtual Disks.  Create a [Btrfs RAID 10](https://btrfs.wiki.kernel.org/index.php/UseCases#How_do_I_create_a_RAID10_striped_mirror_in_Btrfs.3F "btrfs RAID 10") (mirror and stripe) on these four disks. Download one of the Ubuntu 20.04 ISO files onto your Btrfs partition.  Using the [btrfs-replace command](https://btrfs.wiki.kernel.org/index.php/Manpage/btrfs-replace "btrfs-replace"). Add a fifth virtual disk and replace device `/dev/sde` with the new virtual disk.

    a. Run the `btrfs filesystem show` command and capture the output.
    b. Using the UID of the btrfs device created in the previous step, add the mount point to the `/etc/fstab` and add the `nodatacow` attribute. Mount point options are listed here: [btrfs mount-point options](https://btrfs.wiki.kernel.org/index.php/Manpage/btrfs(5) "btrfs mount-point options")

19. Download a copy of Ubuntu 20.04 and when going through the installer, choose the [EXPERIMENTAL erase disk and use zfs](https://ubuntu.com/blog/enhancing-our-zfs-support-on-ubuntu-19-10-an-introduction "ZFS on Ubuntu Root").  Once the install is complete, upon first login, execute the `sudo zpool status` command and capture the output.

20. Using the `wget` command, retrieve this URL: https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/linux-5.11.19.tar.xz

   a. Untar/uncompress this archive.
   b. Tar the directory and compress it using bzip2, make sure to keep the original input
   c. Tar the directory and compress it using gzip, make sure to keep the original input
   d. Tar the directory and compress it using ztd, make sure to keep the original input
   e. Tar the directory and compress it using xz, make sure to keep the original input
