#### 03-add-file-share-s3-mount-point-to-ec2

  go to storage gate way console
   
    Storage Gateway:
      click on (share-0EB7D07C) file share id then bottom will get mount point details

   ![Alt text](<mount point Commands.png>)

      go to app3 
      
      -> cd /

      -> df -hT

      -> mount -t nfs -o nolock,hard 10.0.0.11:/appolo-dhl-prod /s3vol -- this command will resize the attached volume

      -> df -hT

   ![Alt text](resize-volume-to-s3.png)