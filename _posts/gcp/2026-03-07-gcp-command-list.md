---
layout: single
title: GCP commands List
date: 2026-03-07
categories: [GCP]
---

This post list down GCP commands and some docker commands which can be handy during your hands-on and daily work via command line.

## gcloud commands:

```bash
gcloud config list project
gcloud config configurations list
gcloud config configurations activate my-default-configuration
gcloud config list
gcloud config configurations describe my-second-configuration
gcloud compute instances list
gcloud compute instances create
gcloud compute instances create my-first-instance-from-gcloud
gcloud compute instances describe my-first-instance-from-gcloud
gcloud compute instances delete my-first-instance-from-gcloud
gcloud compute zones list
gcloud compute regions list
gcloud compute machine-types list
```

 ```bash
gcloud compute machine-types list --filter zone:asia-southeast2-b
gcloud compute machine-types list --filter "zone:(asia-southeast2-b asia-southeast2-c)"
gcloud compute zones list --filter=region:us-west2
gcloud compute zones list --sort-by=region
gcloud compute zones list --sort-by=~region
gcloud compute zones list --uri
gcloud compute regions describe us-west4
 
gcloud compute instance-templates list
gcloud compute instance-templates create instance-template-from-command-line
gcloud compute instance-templates delete instance-template-from-command-line
gcloud compute instance-templates describe my-instance-template-with-custom-image
```

## Docker commands:

See current running container:
```bash			
      docker ps
```

See containers ran in past which are not running:
```bash
      docker ps -a
abc@golang-server:~$ sudo docker ps -a
CONTAINER ID   IMAGE                                        COMMAND     CREATED        STATUS                    PORTS     NAMES
075aeda8b355   gcr.io/golang-handson-439117/golang-server   "/server"   22 hours ago   Exited (1) 22 hours ago             unruffled_montalcini
```

To debug a container say to find out the log path. use this command and paste the container id from the above command
```bash
			docker inspect <container id>
```

you can run the container in interactive mode to check if the binary actually exists in the expected location. Run the following
```bash
      docker run -it --entrypoint sh gcr.io/golang-handson-439117/golang-server:latest
```

Once inside the container, navigate to the directory and inspect the files:
```bash
      cd /home/abc/project/server/bin
			ls -l
```
To stop the container		
```bash
			docker stop <container id>
```

To deploy the docker image in gcp you need to add your user in the docker first.use this command:
```bash
sudo usermod -aG docker <username>
newgrp docker   ---reload docker with the new user.
```
# GCP Cloud commands

## Compute Engine:

get the available zone list:
```bash
      gcloud compute zones list
```
create VM: 
```bash
      gcloud compute instances create myvm01 --machine-type n1-standard-1
												--provisioning-model spot
												--zone us-central1-f
```									

ssh to the vm:
```bash
      gcloud compute ssh golang-server --project <project-name>
```

create vm using image:
pick an image: 
```bash
      gcloud compute images list
			gcloud compute instances create myvm02 --image-family centos-stream-9 
										--image-project centos-cloud
										--machine-type n1-standard-1
										--zone us-central1-f
```

**STOP VM instance:**
```bash
			gcloud compute instances stop vm02
			gcloud compute instances stop  ch06-instance-1 ch06-instance-2 --async
```
**START VM instance:**
```bash
			gcloud compute instances start vm02
			gcloud compute instances start  instance-1 instance-2 --async
			The async parameter returns immediately without waiting for the operations to complete.
```

**DELETE VM instance:**
```bash
			gcloud compute instances delete vm02 
			gcloud compute instances delete ch06-instance-1 ––zone=us-central1-b
```
When an instance is deleted, the disks on the VM may be deleted or saved by using the --delete-disks and --keep-disks parameters, respectively. You can specify all to keep all disks, boot to specify the partition of the root filesystem, and data to specify nonboot disks. For example, the following command keeps all disks:	
```bash
			gcloud compute instances delete ch06-instance-1 --zone=us-central2-b\ --keep-disks=all
```
while the following deletes all nonboot disks
```bash
			gcloud compute instances delete ch06-instance-1 ––zone=us-central2-b\ --delete-disks=data
```

**View VM list:**

```bash
			gcloud compute instances list 
```			
This command takes an optional name of an instance. To list VMs in a particular zone, you can use the following
```bash
			gcloud compute instances list --filter="zone:ZONE"
```
The --limit parameter is used to limit the number of VMs listed, and the --sort-by parameter is used to reorder the list of VMs by specifying a resource field. You can see the resource fields for a VM by running the following:
```bash
			gcloud compute instances describe	
```

## Working with snapshots:

**Create snapshots:**
```bash
			gcloud compute disks snapshot DISK_NAME --snapshot-names=NAME
```			
where DISK_NAME is the name of a disk and NAME is the name of the snapshot. To view a list of snapshots, use the following:
```bash
			gcloud compute snapshots describe SNAPSHOTS_NAME
```
where SNAPSHOT_NAME is the name of the snapshot to describe. To create a disk, use this:
```bash
			gcloud compute disks create DISK_NAME --source-snapshot=SNAPSHOTS_NAME
```
You can also specify the size of the disk and disk type using the --size and --parameters. Here's an example:
```bash
			gcloud compute disks create disk-1 --source-snapshot=ch06-snapshot --size=100\ --type=pd-standard
```			

**Working with Images:**	
```bash
			gcloud compute images create IMAGE_NAME
```

where IMAGE_NAME is the name given to the images. The source for the images is specified using one of the source parameters, which are as follows:

--source-disk
--source-image
--source-image-family
--source-snapshot
--source-uri
The source-disk, source-image, and source-snapshot parameters are used to create an image using a disk, image, and snapshot, respectively. The source-image-family parameter uses the latest version of an image in the family. Families are groups of related images, which are usually different versions of the same underlying image. The source-uri parameter allows you to specify an image using a web address.

An image can have a description and a set of labels. These are assigned using the --description and --labels parameters.

Here is an example of creating a new image from a disk:
```bash
			gcloud compute images create image-1 ––source-disk=disk-1 
```
You can also delete images when they are no longer needed using this:
```bash
			gcloud compute images delete IMAGE_NAME
      gcloud compute images export --destination-uri=DESTINATION_URI\ ––image=IMAGE_NAME
```

where DESTINATION_URI is the address of a Cloud Storage bucket where you want to store the image.

---

# Instance Group & templates

To create an instance group, you must first create an instance group template																									

**Create instance template:**
```bash
            gcloud compute instance-templates create INSTANCE
```

You can specify an existing VM as the source of the instance template by using the --source-instance parameter
```bash
			gcloud compute instance-templates create instance-template-1 --source-instance=instance-1
```			

**To delete an instance group template:**
```bash
			gcloud compute instance-templates delete Instance-template-name 
```			

**List instance template & instance groups:**

```bash
			gcloud compute instance-templates list
			gcloud compute instance-groups managed list-instances
```

**List instances within a instance group:**
```bash
			gcloud compute instance-groups managed list-instances instance-group-name
```

---

## Modify Instances

**Change Machine type (Vertical scaling)**
```bash
			gcloud compute instances stop vm01
			gcloud compute instances set-machine-type vm01 --machine-type n2-standard-2 
										--zone us-central1-f 
```

for change machine type to a custome machine
```bash
      gcloud compute instances stop vm01
			gcloud compute instances set-machine-type vm01 --machine-type n2-custom-4-2048 
										--zone us-central1-f
```

This command only changes or enhances the machine capacity in terms of cpu, ram. but if you want to increase the disk capacity, use this command.
```bash
			gcloud compute disks resize <disk-name> --size <disk-size> 
										--zone us-central1-f
```
This works foe persistent disk. for any other disk type , it has to be done manually.

---

## Cloud storage commands 

**Create Cloud Bucket:**
```bash
			gsutil mb gs://Bucket_name
```
			***This name must be globally unique***
      
Upload a file or folder from your local device or google VM:
```bash
      gsutil cp [local object location] gs://[Destination bucket name]
```
		ex. gsutil cp /home/abc/readme.txt gs://ace-exam-abc/
    
To download a copy of your data from cloud storage bucket to a directory on a vm, log into the vm using ssh and execute the command:
```bash
			gsutil cp gs://ace-exam-abc/readme.txt /home/abc/project/server/bin
```

Move a file:
```bash
      gsutil mv gs://[source bucket name]/[source object name]  gs://[destination bucket name]/destination object name]
```

Export data from cloud sql in sql format:
```bash
      gcloud sql export sql [dB instance name] gs://[bucket name]/[export_file_name.sql] --database=mysql
```

Export data from cloud sql in csv format:
```bash
      gcloud sql export csv [dB instance name] gs://[bucket name]/[export_file_name.csv] --database=mysql
```
			
Import data to a cloud sql db from sql format file
```bash
      gcloud sql import sql [dB instance name] gs://[bucket name]/[export_file_name.sql] --database=mysql
```

---

## Importing and Exporting Data: Cloud Firestore

Export data from cloudstore in NATIVE mode:
```bash
      gcloud firestore export gs://[bucket name]
```

Export data from firestore in DATASTORE mode:
```bash
			gcloud datastore export --namespaces="(default") gs://[bucket name]
```
			Here the value of namespace was default or we used default namespace.

Import data from a cloud bucket to the firestore in DATASTORE mode:
```bash
			gcloud datastore import gs://[bucket name]/path/[file_name]
																		
ex: 		gcloud datastore export --namespaces="(default)" gs://ace-exam-datastore1
```															
																		
The export process will create a folder named ace-exam-datastore1 using the data and time of the export. The folder will contain a metadata file and a folder containing the exported data. The metadata filename will use the same date and time used for the containing folder. The data folder will be named after the namespace of the exported Datastore database.
```bash
ex:			gcloud datastore import gs://ace-exam-datastore1/2018-12-20T19:13:55_64324/2018-12-20T19:13:55_64324.overall_export_metadata
```

---											
                        
## Importing and Exporting Data: BigQuery

**Extract data from big query:**
```bash
			bq extract --destination_format [FORMAT] --compression [COMPRESSION_TYPE] --field_delimiter [DELIMITER] --print_header 
[BOOLEAN] [PROJECT_ID]:[DATASET].[TABLE] gs://[BUCKET]/[FILENAME]

ex: 
			bq extract --destination_format CSV --compression GZIP 'mydataset.mytable' gs://example-bucket/myfile.zip
```

**load data  into big query:**
```bash
			bq load --autodetect --source:format=[FORMAT] [DATASET].[TABLE] \[PATH_TO_SOURCE]
ex:
			bq load --autodetect --source:format=CSV mydataset.mytable \ gs://ace-exam-biquery/mydata.csv
```			

---

## Importing and Exporting Data: Cloud Spanner

Cloud Spanner does not have a gcloud command to export data, but you can use Dataflow to export data. For more details, see the Cloud Dataflow documentation at https://cloud.google.com/dataflow/docs.

---

## Exporting Data from Cloud Bigtable
												
Bigtable exports are stored in Cloud Storage and can use one of three formats: SequenceFile, Avro, or Parquet. Gigtable stores data in massively scalable tables, each of which is a sorted key/value map. 
configure cbt in Cloud Shell:
```bash
			gcloud components update
			gcloud components install cbt
```
			
Bigtable requires an environment variable called instance to be set by including it in a CBT configuration file called .cbtrc, which is kept in the home directory.
For example, to set the instance to ace-exam-bigtable, enter this command at the command-line prompt:

			echo instance = ace-exam-bigtable>> ~/.cbtrc
Now cbt commands will operate on that instance. To create a table, issue a command such as this:
```bash
			cbt createtable ace-exam-bt-table	
list tables:
			cbt ls
```

Tables contain columns, but Bigtable also has a concept of column families. To create a column family called colfam1:
```bash
			cbt createfamily ace-exam-bt-table colfam1 
```
			
set a value of the cell with the column colfam1 in a row called row1:
```bash			
			cbt set ace-exam-bt-table row1 colfam1:col1=ace-exam-value
display the contents of a table:
			cbt read ace-exam-bt-table
```

---
	
## Importing and Exporting Data: Cloud Dataproc
												
Cloud Dataproc is not a database like Cloud SQL or Bigtable; rather, it is a data analysis platform. These platforms are designed more for data manipulation, statistical analysis, machine learning, and other complex operations than for data storage and retrieval. Cloud Dataproc is not designed to be a persistent store of data. For that you should use Cloud Storage or persistent disks to store the data files you want to analyze.

Cloud Dataproc does have Import and Export commands to save and restore cluster configuration data. These commands are available using gcloud.

The command to export a Dataproc cluster configuration is as follows:
```bash
			gcloud dataproc clusters export [CLUSTER_NAME] \ --destination=[PATH_TO_EXPORT_FILE]
ex:
			gcloud dataproc clusters export ace-exam-dataproc-cluster \ --destination=gs://ace-exam-bucket1/mydataproc.yaml
```

To import a configuration file,
```bash
			gcloud dataproc clusters import [SOURCE_FILE]
ex:
			gcloud dataproc clusters import gs://ace-exam-bucket1/mydataproc.yaml
```

---

## Streaming Data to Cloud Pub/Sub

create topics and subscriptions:
```bash
			gcloud pubsub topics create [TOPIC-NAME]
			gcloud pubsub subscriptions create [SUBSCRIPTION-NAME] ––topic        TOPIC-NAME]													
ex:
			gcloud pubsub topics create ace-exam-topic1
			gcloud pubsub subscriptions create --topic=ace-exam-topic1 ace-exam-sub1 
```

To test whether the message queue is working correctly, you can send data to the topic using the following command:
```bash
			cloud pubsub topics publish topic_name --message message_content
then read that message from the subscription using the following:
			gcloud pubsub subscription pull --auto-ack subscription_name
ex:
			gcloud pubsub topics publish ace-exam-topic1 \ ––message "first ace exam message"
			gcloud pubsub subscriptions pull ––auto-ack ace-exam-sub1


Take ssh
sudo sudo
df-kh
lsblk(logical portion of each partition are visible)
parted /dev/sda (resizing device storage)
resizepart
1 (enter partition number)
yes
quit
lsblk (check to confirm)

command to upload your project files to your google cloud storage bucket:
cd /path-to-your-local-project/golang-server
gsutil cp -r . gs://your-bucket-name/golang-server/
```

another way to build the image is to go to your local machine and in the folder wher code is there. execute this command
```bash
gcloud builds submit --tag gcr.io/golang-handson-439117/golang-server .
```

This should be run from the folder where the docker file is present. 

Now to deploy the docker image in your gcp VM. First step is:


Pull the image from artifactory Registry into your VM:
Go to Artifact registry -> gcr.io->  here against the image there will be three dots, the commands can be copied from there
```bash
gcloud auth configure-docker gcr.io
docker pull \
    gcr.io/golang-handson-439117/golang-server:latest
```	
	
Now to create a VPC for this project,
```bash
  gcloud compute networks create golang-vpc-sachin19183 --project=golang-handson-439117 --description=VPC\ for\ my\ golang\ project --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional --bgp-best-path-selection-mode=legacy
	
	gcloud compute networks subnets create tcp-subnet-8080 --project=golang-handson-439117 --description=subnet\ for\ tcp\ communication\ over\ 8080 --range=10.128.0.0/24 --stack-type=IPV4_ONLY --network=golang-vpc-sachin19183 --region=us-central1 --enable-private-ip-google-access --enable-flow-logs --logging-aggregation-interval=interval-5-sec --logging-flow-sampling=0.5 --logging-metadata=include-all
```

To create a VPN at the command line, you can use these three commands:
```bash
	gcloud compute target-vpn-gateways
	gcloud compute forwarding-rule
	gcloud compute vpn-tunnels
An example is:
	gcloud compute vpn-tunnels create NAME --peer-address=PEER_ADDRESS \--shared-secret=SHARED_SECRET --target-vpn-gateway=TARGET_VPN_GATEWAY
```
NAME is the name of the tunnel. PEER_ADDRESS is the IPv4 address of the remote tunnel endpoint. SHARED_SECRET is a secret string. TARGET_VPN_GATEWAY is a reference to the target VPN gateway IP.

When creating an HA VPN, you will need to specify either the --peer-gcp-gateway or the --peer-external-gateway parameter as well.

The format of gcloud compute forwarding-rule is as follows:
```bash
	gcloud compute forwarding-rules create NAME --TARGET_SPECIFICATION=VPN_GATEWAY
```
The format of the gcloud compute vpn-tunnels command is as follows:
```bash
	gcloud compute vpn-tunnels create NAME --peer-address=PEER_ADDRESS \--shared-secret=SHARED_SECRET --target-vpn-gateway=TARGET_VPN_GATEWAY
```

---
