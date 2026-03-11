# Zesty Disk CFN
This CloudFormation template automates the deployment of an EC2 Auto Scaling Group designed deploy **Zesty Disk** filesystems. It handles infrastructure provisioning, IAM roles, and the automated installation of the Zesty Agent across Linux or Windows environments.

---

##  Overview

The template simplifies the setup by:
* **Dynamic OS Selection:** Support for Amazon Linux 2023, Windows Server 2022, or a **Custom AMI**.
* **Automated Agent Deployment:** Uses `UserData` to fetch and configure the Zesty Agent automatically.
* **Storage Configuration:** Attaches a secondary **50GB gp3 volume** specifically tagged (`ZestyDisk: true`) for management.
* **IAM Compliance:** Includes an Instance Profile with `AmazonSSMManagedInstanceCore` for secure, keyless access via Systems Manager.

---

##  Prerequisites

1.  **Zesty API Key:** Retrieve this from the Zesty Platform (*ZestyDisk > Managed Filesystems > Install Agent*).
2.  **Network Setup:** You will need an existing VPC, Subnet IDs, and a Security Group ID.
3.  **Key Pair:** An existing RSA KeyPair name for initial access if needed.

---

## 📋 Parameters

| Group | Parameter | Description |
| :--- | :--- | :--- |
| **1. AMI Selection** | `AmiSelection` | Select `AmazonLinux2023`, `Windows2022`, or `CustomAMI`. |
| | `CustomAMI` | **(Required if Custom)** The ID of your AMI (e.g., `ami-xxxxxx`). |
| | `CustomOSType` | **(Required if Custom)** Specify `Linux` or `Windows`. |
| **2. Instance Config** | `ZestyApiKey` | Your Zesty Agent API Key (Sensitive/Hidden). |
| | `InstanceType` | EC2 family (Default: `c5a.large`). |
| | `InstanceCount` | Number of instances to launch. |
| **3. Network** | `SubnetIds` | List of subnets for the ASG. |
| | `SecurityGroupId` | Security group to attach to instances. |

---

## 💻 Deployment

### Option 1: AWS CLI (Standard AMI)
To deploy using the latest **Amazon Linux 2023** image:

```bash
aws cloudformation create-stack \
  --stack-name Zesty-POC-Stack \
  --template-body file://zesty-poc.yaml \
  --parameters \
    ParameterKey=AmiSelection,ParameterValue=AmazonLinux2023 \
    ParameterKey=ZestyApiKey,ParameterValue=YOUR_API_KEY \
    ParameterKey=SubnetIds,ParameterValue=subnet-123,subnet-456 \
    ParameterKey=SecurityGroupId,ParameterValue=sg-000 \
    ParameterKey=KeyPairName,ParameterValue=my-key-pair \
  --capabilities CAPABILITY_IAM
```
For CustomAMI
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
### Run the following command to see the mount point as mounted
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
