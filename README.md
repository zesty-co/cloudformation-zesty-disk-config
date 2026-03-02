# Zesty-Disk-POC (CloudFormation)

This repository provides an automated AWS CloudFormation template for a **Zesty Disk POC**. It handles infrastructure setup, dynamic volume tagging, and agent installation using native AWS parameters for maximum flexibility.



## Overview

This template deploys a customizable EC2 environment optimized for Zesty storage management. By using CloudFormation parameters, you can tailor the instance to match your specific testing requirements without editing the source code.

### Key Features
* **Fully Parameterized**: Control Instance Type, Root Disk Size, and the specific Mount Path from the AWS Console.
* **Automated EBS Tagging**: The instance self-tags its secondary volume with `ZestyDisk: true` using a scoped IAM role.
* **Storage Configuration**: 
    * **Root**: Customizable size (gp3, encrypted).
    * **Data**: 30GB gp3 (encrypted, automatically mounted to your chosen path).
* **Security**: Enforces IMDSv2 for secure metadata access.
* **OS**: AmazonLinux 2.

---

## Parameters

| Parameter | Default | Description |
| :--- | :--- | :--- |
| **ZestyApiKey** | - | **(Required)** Your unique Zesty Agent Key. |
| **InstanceType** | `c5a.large` | The EC2 instance family to use. |
| **RootVolumeSize**| `8` | Size in GB for the primary OS drive. |
| **MountDirectory**| `/zd_directory` | The Linux path where the second disk will be mounted. |
| **SubnetIds** | - | The subnets where the instance will reside. |
| **SecurityGroupId**| - | Existing Security Group for network access. |

---

## Deployment Instructions

### Method 1: AWS Management Console
1. Upload `template.yaml` to the CloudFormation console.
2. Fill in the parameters, specifically your **API Key** and desired **Mount Path**.
3. Acknowledge the IAM capability and click **Create Stack**.

### Method 2: AWS CLI
```bash
aws cloudformation create-stack \
  --stack-name Zesty-POC-Stack \
  --template-body file://template.yaml \
  --parameters \
      ParameterKey=ZestyApiKey,ParameterValue="YOUR_KEY" \
      ParameterKey=MountDirectory,ParameterValue="/data" \
      ParameterKey=RootVolumeSize,ParameterValue="20" \
      ParameterKey=SubnetIds,ParameterValue="subnet-1,subnet-2" \
      ParameterKey=SecurityGroupId,ParameterValue="sg-123" \
  --capabilities CAPABILITY_IAM




⚠️ Important Note on Cleanup
The EBS volumes in this POC are configured with DeleteOnTermination: false. This is to prevent accidental data loss. When you are finished with the POC and delete the CloudFormation stack, you must manually delete the EBS volumes in the AWS Console to stop incurring costs.
