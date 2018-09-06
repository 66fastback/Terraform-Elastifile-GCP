# Terraform-Elastifile-GCP

Terraform to create, configure and deploy a Elastifile Cloud Filesystem (ECFS) cluster in Google Compute (GCE)

## Note:
Follow the Elastifile Cloud Deployment GCP Installation Guide to make sure ECFS can be successfully deployed in GCE before using this.

## Use:
1. Create password.txt file with a password to use for eManage  (.gitignore skips this file)
2. Specify configuration variables in terraform.tfvars:
- TEMPLATE_TYPE = small, medium, standard, custom. Only use custom in consultation with Elastifile support
- NUM_OF_VMS = Number of ECFS virtual controllers, 3 minimum for small/medium, 6 minimum for standard
- USE_LB = true/false. true to let EMS setup a single google load balancer address for client NFS connections (Recommended H/A configuration), must be false when using shared VPC subnetwork
- DISK_TYPE = local, ssd, or hdd. Only applies to custom templates
- DISK_CONFIG = [disks per vm]_[disk size in GB] example: "8_375" will create 8, 375GB disks. Only applies to custom templates
- VM_CONFIG = [cpu cores per vm]_[ram per vm] example "20_128" will create 20 CPU, 128GB RAM VMs. Default: "4_42" Only applies to custom templates
- CLUSTER_NAME = Name for ECFS service, no longer than
- ZONE = Zone
- PROJECT = Project name
- SUBNETWORK = Subnetwork to use. default or full path to use specific/custom project or shared vpc subnetwork eg projects/support-team-172804/regions/us-west1/subnetworks/andrew-shared-vpc-network-subnet
- IMAGE = EMS image name
- CREDENTIALS = path to service account credentials .json file if not using
- SERVICE_EMAIL = service account email address
3. Run 'terraform init' then 'terraform apply'


## Components:

**google_ecfs.tf**
Main terraform configuration file.

**create_vheads.sh**
Bash script to configure Elastifile eManage (EMS) Server via Elastifile REST API. EMS will deploy cluster of ECFS virtual controllers (vheads). Called as null_provider from google_ecfs.tf

Note: REST calls are HTTPS (443) to the public IP of EMS. Ensure GCP project firewall rules allow 443 (ingress) from wherever this Terraform template is run.

**destroy_vheads.sh**
Bash script to query and delete multiple GCE instances and network resources simultaneously. Called as null_provider destroy from google_ecfs.tf

**password.txt**
Plaintext file with EMS password

## Troubleshooting:
**create_vheads.log**
Output of REST commands

**/elastifile/log/**
Log directory from EMS

## Known Issues:
Custom template configurations are not officially supported by Elastifile
