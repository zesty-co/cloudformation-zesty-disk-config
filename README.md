# Zesty Disk POC (CloudFormation)

Accelerate your Zesty Disk evaluation. This repository provides an AWS CloudFormation template that automates infrastructure setup and ZD agent installation across **Amazon Linux 2023** and **Windows Server 2022**.



---

## Overview

This template deploys a scalable EC2 environment using an Auto Scaling Group (ASG). It ensures that each instance launched in the AWS account environment includes automated installation and setup of Zesty's optimization tool - ZestyDisk.

The template enables consistent infrastructure provisioning while automating instance creation and disk configuration for the ZestyDisk.

##  Key Features

* **Multi-OS Selection:** Seamlessly toggle between **Amazon Linux 2023** and **Windows Server 2022**.
* **Native NVMe Support:** Optimized for the `c5a` instance family, automatically targeting `/dev/nvme1n1` (Linux) or Disk 1 (Windows).
* **Automated Tagging:** Uses `TagSpecifications` to ensure EBS volumes are created with `ZestyDisk: true` and `Name: ZestyPOC` tags—essential for Zesty's automation.
* **IMDSv2 Enforced:** High-security metadata service configuration.
* **SSM Ready:** Includes IAM roles for **AWS Systems Manager**, allowing for browser-based RDP/SSH via Fleet Manager or Session Manager.

---

##  Parameters

| Parameter | Default | Description |
| :--- | :--- | :--- |
| **OSSelection** | `AmazonLinux2023` | Choose `AmazonLinux2023` or `Windows2022`. |
| **ZestyApiKey** | - | **(Required)** Your unique Zesty Agent API Key. |
| **InstanceCount** | `1` | Number of instances to launch (Max 5). |
| **InstanceType** | `c5a.large` | The EC2 instance family (NVMe optimized). |
| **SubnetIds** | - | **(Required)** Target subnets for deployment. |
| **SecurityGroupId** | - | **(Required)** Security Group for network access. |
| **KeyPairName** | - | **(Required)** Existing RSA Key Pair for instance access. |

---

##  Deployment Instructions

### Method 1: AWS Management Console
1.  Download the `ZD_CFN.yaml` from this repo.
2.  Navigate to **CloudFormation** in the AWS Console.
3.  Click **Create Stack** > **With new resources (standard)**.
4.  Upload the template, fill in the parameters, and click **Create Stack**.

### Method 2: AWS CLI
```bash
aws cloudformation create-stack \
  --stack-name Zesty-POC-Stack \
  --template-body file://template.yaml \
  --parameters \
      ParameterKey=OSSelection,ParameterValue="AmazonLinux2023" \
      ParameterKey=ZestyApiKey,ParameterValue="YOUR_KEY_HERE" \
      ParameterKey=SubnetIds,ParameterValue="subnet-12345abc" \
      ParameterKey=SecurityGroupId,ParameterValue="sg-09876xyz" \
      ParameterKey=KeyPairName,ParameterValue="my-rsa-key" \
  --capabilities CAPABILITY_IAM

```
## Run the command to generate a big file
#### Connect to the ec2
#### Each value means the following <file_size_in_GB> <file_path> <delete_file y/n>
```
sudo su

./zesty-big-file-generator.sh 30 /zd_directory/bigfiledata.txt n
```
## For Windows, use the following URL to download the dig data file
https://ash-speed.hetzner.com/

#### Select the file for downloading - save the file to disk D

