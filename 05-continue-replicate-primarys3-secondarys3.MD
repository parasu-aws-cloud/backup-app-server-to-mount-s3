#### continue-replicate-primarys3-secondarys3:

3. go to s3 console

  *  click on (appolo-dhl-prod)
    
    management: option
      
      * Lifecycle rules : if you want
      
      * Replication rules : click on (create replication rules)

         Replication rule configuration:
          
         Replication rule name: appolodhl
         
         Status: [select] enable

         Priority: 0

      * Source bucket

          Source bucket name: appolo-dhl-prod

          Source Region: Asia Pacific (Singapore) ap-southeast-1

          Choose a rule scope: [select] Limit the scope of this rule using one or more filters

          Filter type: You can filter objects by prefix, object tags, or a combination of both.

          Prefix: if you replicate specific object mention here or now we don't want to replicate at this movement

          Tags: key: Name     value: appolo-dhl-prod-replication

      * Destination
          
          Destination: [select] Choose a bucket in this account

          Bucket name: click on (browse-s3)

            Choose a bucket:
              S3 Buckets: [select] secondery-bucket

              click on (choose-path)

          Destination Region: 

      * IAM role

          [select] Choose from existing IAM roles

      * Encryption
        
        note: Server-side encryption protects data at rest.

          [select] Replicate objects encrypted with AWS Key Management Service (AWS KMS)

          AWS KMS keys for decrypting source objects (1): [select] aws/s3 default aws kms key encryption

      * Destination storage class
        
          [select] Change the storage class for the replicated objects

          Storage class: [select] One Zone-IA


      * Additional replication options

          [select] Replication Time Control (RTC)
          note: Replication Time Control replicates 99.99% of new objects within 15 minutes and includes replication metrics. Additional fees will appl

          [select] Replica modification sync
          note: Replicate metadata changes made to replicas in this bucket to the destination bucket

        click on save button

        note: Replication configuration successfully updated



           
            


          
            
