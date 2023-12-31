### go to ec2 console and create app-server(ec2) configure any thing you want.

  1. create (app-server1)

     login into servver and do this:

      -- sudo vi /etc/hostname = app-server1 

      -- init 6 (this command will reboot the server)  
    
  2. create same copy from that server (app-server2).
     
     login into servver and do this:

      -- sudo vi /etc/hostname = app-server2 

      -- init 6 (this command will reboot the server) 
 
  3. create one vpn server and with that sg map two above server private-ips then can able to access the servers.
     
    * connect or access via vpn-server or eip.

  4. create external volume (2GB) using by S3 bucket can replace external volume size.

  note: here understand what are the file system for linux.
      
       -- Ext4 (Fourth Extended File System): This is the default file system for many Linux distributions. It's an improvement over Ext3 and provides better performance and reliability. Ext4 supports large file sizes and volumes, and it allows for features such as journaling, which helps in faster crash recovery.

       -- Ext3 (Third Extended File System): An enhanced version of Ext2, Ext3 includes journaling, which helps in faster file system recovery in the event of a crash. It provides backward compatibility with Ext2 and offers better data reliability and performance.

       -- XFS (X File System): XFS is a high-performance journaling file system that is particularly well-suited for large files and large storage volumes. It provides excellent scalability and is optimized for parallel I/O, making it a good choice for servers and storage devices.

       -- Btrfs (B-Tree File System): Btrfs is a modern copy-on-write file system that supports advanced features such as snapshots, pooling, and checksums for data integrity. It is designed for better data management, fault tolerance, and easy administration of large-scale storage.

       -- JFS (Journaled File System): JFS is a journaling file system with a focus on high performance and reliability. It is known for its low CPU and memory usage and is often used in enterprise-level storage systems.

       -- ReiserFS: ReiserFS is a journaling file system that is known for its high performance, especially with small files. It has support for metadata journaling and efficient handling of small files, making it suitable for specific use cases where performance with small files is crucial.

       -- F2FS (Flash-Friendly File System): F2FS is optimized for flash-based storage devices such as solid-state drives (SSDs) and eMMC. It is designed to improve performance and reduce write amplification on flash storage, making it an ideal choice for devices where flash memory is the primary storage medium.
       
       login into servers:
       
        * first with into root (sudo su - root)

           -- du -sh

           -- du -hP

           -- go to root folder : cd /

           -- lsblk

           -- du -sh

           -- df

           -- df -hT

        do all activities
  
  note: if volume ans ec2 are same zone we can attach dettach. if across the zone we need to take snapshoot from the volume 
  from that snapshot we can create volume then attach and dettach respectivve ec2 available zone.

 * lets create volumes respective zones.

   1. go to ec2 console dashboard

     click on (create-volume)
     
     steps:

     * Create volume:
      
       Volume type: general purpose SSD (gp2)

       Size (GiB) : 2

       IOPS : 300 / 3000

       Throughput (MiB/s) : not applicable

       Availability Zone : eu-west-2a

       Snapshot ID - optional : none

       Encryption : [select] Encrypt this volume

       KMS key : (default) aws/ebs
       
       auto fetch details after selecting kms key
       
       KMS key description : Default key that protects my EBS volumes when no other key is defined
       
       KMS key owner : 052492120851 (This account)
       
       KMS key ID : 0c7743ec-1754-4a19-9dbf-aecbb320111a
       
       KMS key ARN : arn:aws:kms:eu-west-2:052492120851:key/0c7743ec-1754-4a19-9dbf-aecbb320111a
    
       Tags - optional

       Key : name        value : app1-ex

     * these volumes need to attach app-server1 ,app-server2

        steps:

         [select] volume1 goto action click on attach volume

          Attach volume:

           Basic details 
           
              Volume ID : vol-05ddfe77477d7e368

              Availability Zone : eu-west-2a

              Instance : app1

              Device name : /dev/sdf

              and click on attach volume.
      
      note: lets check it attached or not 
        
        give this command:
         
         command :  lsblk -- it is going to show list of volumes 

                    [root@ip-10-0-0-14 /]# lsblk
            NAME      MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
            xvda      202:0    0   8G  0 disk 
            ├─xvda1   202:1    0   8G  0 part /
            ├─xvda127 259:0    0   1M  0 part 
            └─xvda128 259:1    0  10M  0 part /boot/efi
            xvdf      202:80   0   2G  0 disk 
           
       note : see hete 2GB attached as external volume to ec2 and we need to make it file sytem

        following commands:

         -> df -hT -- this command will show all types of volume file system types

         -> mkfs -t ext4 /dev/xvdf -- it make that volume as a file system

         output:

         Creating filesystem with 524288 4k blocks and 131072 inodes
         Filesystem UUID: e382cabc-0099-4091-a5bc-e808c53f3b16
         Superblock backups stored on blocks: 
              32768, 98304, 163840, 229376, 294912

         Allocating group tables: done                            
         Writing inode tables: done                            
         Creating journal (16384 blocks): done
         Writing superblocks and filesystem accounting information: done

         -> mkdir -p /s3vol -- creating directory

         -> mount /dev/xvdf /s3vol/ -- this /dev/xvdf mounted to /s3vol

         -> df -hT -- it will show now file systen

         output: note: now it is showing

         Filesystem     Type      Size  Used Avail Use% Mounted on
         devtmpfs       devtmpfs  4.0M     0  4.0M   0% /dev
         tmpfs          tmpfs     475M     0  475M   0% /dev/shm
         tmpfs          tmpfs     190M  2.9M  188M   2% /run
         /dev/xvda1     xfs       8.0G  1.5G  6.5G  19% /
         tmpfs          tmpfs     475M     0  475M   0% /tmp
         /dev/xvda128   vfat       10M  1.3M  8.7M  13% /boot/efi
         tmpfs          tmpfs      95M     0   95M   0% /run/user/1000
         /dev/xvdf      ext4      2.0G   24K  1.8G   1% /s3vol

    note: when you reboot the server converted volume as file system back normol
    for that you need to do two more steps.

    steps: very importent
    ---------------------
    1. vi /etc/fstab -- in this file need to register volume 2GB

    2. (/dev/xvdf                                     /s3vol      ext4   defaults,noatime  1   1) place this in that file
       
       and save it.
    3. note: now even though server rebooted your volume and file system exist or perminettly mounted.
    
    -- df -hT -- it will show file system types server

         Filesystem     Type      Size  Used Avail Use% Mounted on
         devtmpfs       devtmpfs  4.0M     0  4.0M   0% /dev
         tmpfs          tmpfs     475M     0  475M   0% /dev/shm
         tmpfs          tmpfs     190M  2.9M  188M   2% /run
         /dev/xvda1     xfs       8.0G  1.5G  6.5G  19% /
         tmpfs          tmpfs     475M     0  475M   0% /tmp
         /dev/xvda128   vfat       10M  1.3M  8.7M  13% /boot/efi
         tmpfs          tmpfs      95M     0   95M   0% /run/user/1000
         /dev/xvdf      ext4      2.0G   24K  1.8G   1% /s3vol