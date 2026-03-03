# Zesty-Disk-POC (CloudFormation)

Accelerate your Zesty Disk evaluation. This repository provides a fully-parameterized CloudFormation template that automates infrastructure setup, volume tagging, and agent installation across multiple instances.



## Overview

This template deploys a scalable EC2 environment. It uses an **Auto Scaling Group (ASG)** to manage instance lifecycle, ensuring that every node in your POC is identically configured with Zesty's optimization tools.

### Key Features
* **Scalable Deployment**: Choose exactly how many instances to launch for your test.
* **Flexible Configuration**: Define Instance Type, Root Disk Size, and Mount Paths via parameters.
* **Automated EBS Tagging**: Every instance self-tags its 30GB data volume with `ZestyDisk: true`.
* **Security & Performance**: Enforces IMDSv2 and utilizes `gp3` volumes for high performance.

---

## Parameters

| Parameter | Default | Description |
| :--- | :--- | :--- |
| **ZestyApiKey** | - | **(Required)** Your unique Zesty Agent Key. |
| **InstanceCount** | `1` | Total number of instances to launch. |
| **InstanceType** | `c5a.large` | The EC2 instance family to use. |
| **RootVolumeSize**| `8` | Size in GB for the primary OS drive. |
| **MountDirectory**| `/zd_directory` | Linux path where the second disk will be mounted. |
| **SubnetIds** | - | Target subnets for deployment. |
| **SecurityGroupId**| - | Security Group for network access. |
| **EnableEncryption** | `false` | Enables AES-256 encryption on all EBS volumes. |
| **DeleteOnTermination** | `true` | Set to `true` to auto-clean disks when the stack is deleted. |
| **KeyPairName** | - | **(Required)** The name of your AWS SSH Key Pair. |

---

## Deployment Instructions

### Method 1: AWS Management Console
1. Upload `template.yaml` to the CloudFormation console.
2. Enter your **Zesty API Key** and set the **InstanceCount**.
3. Acknowledge IAM capabilities and create the stack.

### Method 2: AWS CLI
```bash
aws cloudformation create-stack \
  --stack-name Zesty-POC-Stack \
  --template-body file://template.yaml \
  --parameters \
      ParameterKey=ZestyApiKey,ParameterValue="YOUR_KEY" \
      ParameterKey=InstanceCount,ParameterValue="1" \
      ParameterKey=InstanceType,ParameterValue="c5a.large" \
      ParameterKey=MountDirectory,ParameterValue="/zesty_directory" \
      ParameterKey=SubnetIds,ParameterValue="subnet-1,subnet-2" \
      ParameterKey=SecurityGroupId,ParameterValue="sg-123" \
  --capabilities CAPABILITY_IAM

```
## Run the command
## Each value means the following <file_size_in_GB> <file_path> <delete_file y/n>
./zesty-big-file-generator.sh 30 /mnt/data/test.txt n

