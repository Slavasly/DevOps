## Task: LAMP in GCP

There are 2 steps to create LAMP:

1. Create instance with Nginx/Apache + PHP;
2. Create database.

### 1. Create resources via UI:

Instance:

<img src="https://user-images.githubusercontent.com/79985930/208495837-86990bbb-ebcf-4c29-9fa9-339b2e8d0ca5.png" >

Database:

<img src="https://user-images.githubusercontent.com/79985930/208495854-e19b9b09-11ee-4123-924d-bc2631565ee3.png" >

As the resoult of workig LAMP is installed Wordpress v5.9. It available by the link [http://34.159.12.12/](http://34.159.12.12/)

<img src="https://user-images.githubusercontent.com/79985930/208496730-370b4a9f-5460-4c70-964e-7c5040fee759.png" width="500" height="150" >

### 2. Create resources via Terraform

The Terraform script is available by the link: [https://github.com/AndrukhivAndriy/DevOps-BaseCamp/blob/ccf9695fccf6c2f05d5545ea1db3909149a1a38e/main.tf](https://github.com/AndrukhivAndriy/DevOps-BaseCamp/blob/ccf9695fccf6c2f05d5545ea1db3909149a1a38e/main.tf)

To run this script:

- install Terraform [https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- Create some folder on your disk and copy terraform script to this folder;
- run command in shell: `terraform plan` and check is everything is fine;
- run command in shell: `terraform apply`;
- check created resources. 

### 3. Create resources via Gcloud

  
Gcloud commands to create infrastructure are available by the link - [https://github.com/AndrukhivAndriy/DevOps-BaseCamp/blob/974df4ff2e16e76e3477f211960bd7e99b3254e1/gcloud.sh](https://github.com/AndrukhivAndriy/DevOps-BaseCamp/blob/974df4ff2e16e76e3477f211960bd7e99b3254e1/gcloud.sh)

To run this script:
- install Gcloud [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install);
- run commands from script one by one in shell or issue this script as shell script 