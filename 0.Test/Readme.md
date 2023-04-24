# Homework

- Create a Google Cloud account
- Create EC2 instance
- Install terraform
- Launch simple web application with DB usage 

#### Order of execution and useful links:
###### PART 1-2: Overview, Compute & Resiliency
- [LAMP Main TASK](https://cloud.google.com/community/tutorials/setting-up-lamp)
- [Google cloud platform](https://cloud.google.com/docs/get-started)  
- [Create account cloudskillsboost](https://www.cloudskillsboost.google/) 
- [Install the gcloud CLI](https://cloud.google.com/sdk/docs/install)
- [Google credentials](https://cloud.google.com/docs/authentication/application-default-credentials)
- [Install terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- [90-day, $300 Free Trial offer](https://cloud.google.com/free/docs/free-cloud-features#free-trial)
- [Create instance lab](https://www.cloudskillsboost.google/focuses/3563?parent=catalog)
###### PART 3-4: Storage, Network Security
- [Create bucket lab](https://www.cloudskillsboost.google/focuses/3483?catalog_rank=%7B%22rank%22%3A1%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=20665614)
- Upload images to bucket
- Bucket enable versions
- Generate public link for file
- [Create custom Firewall rule](https://cloud.google.com/vpc/docs/using-firewalls)
###### PART 5: Identity and Access Management
- [Create roles bucket access](https://cloud.google.com/iam/docs/creating-custom-roles) 
- [Create Service account](https://cloud.google.com/iam/docs/creating-managing-service-accounts)
- Get files from bucket by gcloud cli
###### PART 6: Monitoring
- [Cloud Monitoring](https://www.cloudskillsboost.google/focuses/10599?catalog_rank=%7B%22rank%22%3A1%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=20666030)
- [Logs and Alerts](https://www.cloudskillsboost.google/focuses/619?parent=catalog)
###### PART 7: Database
- [Launch Database instance](https://cloud.google.com/sql/docs/mysql/create-instance) 
- Launch VM instance 
- Create Mysql User
- Launch simple web application with DB usage 
- [Lab](https://www.cloudskillsboost.google/focuses/2802?parent=catalog)

# Task: Create LAMP in GCP

## There are 2 steps to create LAMP:

https://cloud.google.com/community/tutorials/setting-up-lamp

Create instance with Nginx/Apache + PHP;<br>
Create database.
1. Create resources via UI:<br>
Instance:

Database:

As the resoult of workig LAMP is installed Wordpress v5.9. It available by the link http://34.159.12.12/


2. Create resources via Terraform
The Terraform script is available by the link: https://github.com/AndrukhivAndriy/DevOps-BaseCamp/blob/ccf9695fccf6c2f05d5545ea1db3909149a1a38e/main.tf

To run this script:

Install Terraform https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli

Create some folder on your disk and copy terraform script to this folder;

run command in shell: `terraform plan` and check is everything is fine;
run command in shell: `terraform apply`;<br>
Check created resources.
# 3. Create resources via Gcloud

1.Create VM with code

Gcloud commands to create infrastructure are available by the link - https://github.com/AndrukhivAndriy/DevOps-BaseCamp/blob/974df4ff2e16e76e3477f211960bd7e99b3254e1/gcloud.sh

`gcloud compute instances create lamp-wp --project=kubernetes-376016 --zone=europe-west1-b --machine-type=e2-micro --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=108258132538-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server --create-disk=auto-delete=yes,boot=yes,device-name=lamp-wp,image=projects/debian-cloud/global/images/debian-11-bullseye-v20221206,mode=rw,size=10,type=projects/kubernetes-376016/zones/europe-west1-b/diskTypes/pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any`

To run this script:

install Gcloud https://cloud.google.com/sdk/docs/install;

run commands from script one by one in shell or issue this script as shell script
