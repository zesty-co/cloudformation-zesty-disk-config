# Zesty Disk CFN

This repository contains a CloudFormation template designed to automate the deployment of a **Zesty Disk**. It handles the infrastructure setup, IAM roles, and automated agent installation for both Linux and Windows environments.



---

## Overview

The template provisions an EC2 Auto Scaling Group (ASG) that automatically configures your instances to be managed by Zesty. Key features include:

* **Multi-OS Support:** Amazon Linux 2023, Windows Server 2022, or Custom AMIs.
* **Granular Storage Control:** Define volume sizes and types (`gp2`, `gp3`, `io1`, `io2`) for both Root and Zesty disks.
* **Security First:** Integrated IAM roles for SSM access and optional EBS encryption.
* **Zero-Touch Install:** Bootstrap scripts automatically install the Zesty Agent and mount the managed disks.

---

## Prerequisites

Before deploying the stack, ensure you have:
1.  **Zesty API Key:** Found in the Zesty Platform under *ZestyDisk > Managed Filesystems > Install Agent*.
2.  **Network Info:** Existing VPC Subnet IDs and a Security Group ID.
3.  **EC2 Key Pair:** An existing RSA KeyPair name for instance access.

---

## Parameter Reference

### 1. AMI Selection
| Parameter | Default | Description |
| :--- | :--- | :--- |
| `AmiSelection` | `AmazonLinux2023` | Select `AmazonLinux2023`, `Windows2022`, or `CustomAMI`. |
| `CustomAMI` | - | **Required** if `AmiSelection` is `CustomAMI`. |
| `CustomOSType` | - | **Required** if `CustomAMI` is used (`Linux` or `Windows`). |

### 2. Storage Configuration
| Parameter | Default | Description |
| :--- | :--- | :--- |
| `RootVolumeSize` | `15` | **Note:** Use `100` for Windows instances. |
| `RootVolumeType` | `gp3` | Options: `gp2`, `gp3`, `io1`, `io2`. |
| `ZestyVolumeSize` | `50` | Size of the secondary Zesty-managed disk. |
| `ZestyVolumeType` | `gp3` | Options: `gp2`, `gp3`, `io1`, `io2`. |
| `EncryptVolumes` | `false` | Enable EBS encryption for both volumes. |
| `DeleteVolumesOnTermination` | `true` | Delete volumes when the instance is terminated. |

---
##  Deployment Instructions

### Method 1: AWS Management Console
1.  Download the `ZD_CFN.yaml` from this repo.
2.  Navigate to **CloudFormation** in the AWS Console.
3.  Click **Create Stack** > **With new resources (standard)**.
4.  Upload the template, fill in the parameters, and click **Create Stack**.

## Method 2: AWS CLI

### Deployment Examples

### Standard Linux Deployment
```bash
aws cloudformation create-stack \
  --stack-name Zesty-Linux-POC \
  --template-body file://template.yaml \
  --parameters \
    ParameterKey=ZestyApiKey,ParameterValue="YOUR_API_KEY" \
    ParameterKey=SubnetIds,ParameterValue="subnet-12345,subnet-67890" \
    ParameterKey=SecurityGroupId,ParameterValue="sg-54321" \
    ParameterKey=AmiSelection,ParameterValue="AmazonLinux2023" \
    ParameterKey=ZestyVolumeSize,ParameterValue=100 \
  --capabilities CAPABILITY_IAM
```
### Standard Windows Deployment
```
aws cloudformation create-stack \
  --stack-name Zesty-Windows-POC \
  --template-body file://template.yaml \
  --parameters \
    ParameterKey=ZestyApiKey,ParameterValue="YOUR_API_KEY" \
    ParameterKey=SubnetIds,ParameterValue="subnet-12345" \
    ParameterKey=SecurityGroupId,ParameterValue="sg-54321" \
    ParameterKey=AmiSelection,ParameterValue="Windows2022" \
    ParameterKey=RootVolumeSize,ParameterValue=100 \
    ParameterKey=ZestyVolumeSize,ParameterValue=200 \
  --capabilities CAPABILITY_IAM
```
### CustomAMI Deployment
```
aws cloudformation create-stack \
  --stack-name Zesty-Custom-POC \
  --template-body file://zesty-poc.yaml \
  --parameters \
    ParameterKey=AmiSelection,ParameterValue=CustomAMI \
    ParameterKey=CustomAMI,ParameterValue=ami-0123456789abcdef0 \
    ParameterKey=CustomOSType,ParameterValue=Linux \
    ParameterKey=ZestyApiKey,ParameterValue=YOUR_API_KEY \
    ParameterKey=SubnetIds,ParameterValue=subnet-123 \
    ParameterKey=SecurityGroupId,ParameterValue=sg-000 \
    ParameterKey=KeyPairName,ParameterValue=my-key-pair \
  --capabilities CAPABILITY_IAM

```
## Run the following command to see the mount point as mounted
```
sudo su
```
```
df -h
```
## Run the command to generate a big file
#### Connect to the ec2
#### Each value means the following <file_size_in_GB> <file_path> <delete_file y/n>
```
./zesty-big-file-generator.sh 30 /zd_directory/bigfiledata.txt n
```
## For Windows, use the following URL to download the dig data file
https://ash-speed.hetzner.com/

#### Select the file for downloading - save the file to disk D
