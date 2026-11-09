
<div align="center">

# 🏗️ 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐏𝐫𝐨𝐣𝐞𝐜𝐭 𝐒𝐭𝐫𝐮𝐜𝐭𝐮𝐫𝐞 & 𝐒𝐭𝐚𝐭𝐞
### 𝘖𝘳𝘨𝘢𝘯𝘪𝘻𝘦 𝘠𝘰𝘶𝘳 𝘊𝘰𝘥𝘦, 𝘜𝘯𝘥𝘦𝘳𝘴𝘵𝘢𝘯𝘥 𝘚𝘵𝘢𝘵𝘦 𝘍𝘪𝘭𝘦𝘴 𝘢𝘯𝘥 𝘚𝘦𝘵 𝘜𝘱 𝘢 𝘙𝘦𝘮𝘰𝘵𝘦 𝘉𝘢𝘤𝘬𝘦𝘯𝘥 𝘸𝘪𝘵𝘩 𝘚3

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20S3%20%7C%20DynamoDB-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![State](https://img.shields.io/badge/Remote-State%20%26%20Locking-0A66C2?style=for-the-badge)
![Region](https://img.shields.io/badge/Region-ap--south--1-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)

*A practical guide to structuring a Terraform project and managing its state* ☁️

</div>

---

## 📑 Table of Contents

| # | Topic | Description |
|:-:|-------|-------------|
| 1️⃣ | [Project Files Overview](#1️⃣-create-terraform-project-files) | What each `.tf` file is for |
| 2️⃣ | [Create the Project Folder](#2️⃣-create-the-project-folder) | `mkdir` and `cd` |
| 3️⃣ | [provider.tf](#3️⃣-providertf) | Provider configuration |
| 4️⃣ | [variables.tf](#4️⃣-variablestf) | Input variables |
| 5️⃣ | [terraform.tfvars](#5️⃣-terraformtfvars) | Variable values |
| 6️⃣ | [main.tf](#6️⃣-maintf) | Main resources |
| 7️⃣ | [outputs.tf](#7️⃣-outputstf) | Output values |
| 8️⃣ | [Run Terraform Commands](#8️⃣-run-terraform-commands) | `init`, `plan`, `apply`, `destroy` |
| 9️⃣ | [Terraform Output Commands](#9️⃣-terraform-output-commands) | Read values from state |
| 🔟 | [Destroy a Particular Resource](#-destroy-a-particular-resource) | Using `-target` |
| 1️⃣1️⃣ | [State File Locking](#1️⃣1️⃣-what-is-terraform-state-file-locking-mechanism) | Preventing simultaneous changes |
| 1️⃣2️⃣ | [Terraform State File](#1️⃣2️⃣-what-is-terraform-state-file) | Desired vs actual state |
| 1️⃣3️⃣ | [Without State](#1️⃣3️⃣-what-happens-without-state) | Why state matters |
| 1️⃣4️⃣ | [Inside terraform.tfstate](#1️⃣4️⃣-whats-inside-terraformtfstate) | Contents and security |
| 1️⃣5️⃣ | [Types of State File](#1️⃣5️⃣-types-of-terraform-state-file) | Local vs remote |
| 1️⃣6️⃣ | [S3 Bucket & DynamoDB Table](#1️⃣6️⃣-create-s3-bucket--dynamodb-table-to-keep-tfstate-file) | Backend resources |
| 1️⃣7️⃣ | [Remote Backend Configuration](#1️⃣7️⃣-terraform-remote-backend-configuration) | `backend.tf` |

---

## 1️⃣ Create Terraform Project Files

> 📂 These are Terraform project files used to organize infrastructure-as-code. Terraform automatically reads all `.tf` files in a folder as **one configuration**, so the file names are mainly for **organization and readability**.

| 📄 File | 🎯 Purpose |
|--------|-----------|
| `provider.tf` | Provider configuration |
| `variables.tf` | Input variables |
| `terraform.tfvars` | Variable values |
| `main.tf` | Main resources |
| `outputs.tf` | Outputs |

---
| File             | Purpose                                                                                                                                                               | Example content                                               |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------| ------------------------------------------------------------- |
| provider.tf      | Configures the cloud provider Terraform will use, such as AWS, Azure, or Google Cloud. It can also set the Region.                                                    | provider "aws" { region = "ap-south-1" }                      |
| variables.tf     | Declares input variables so you can reuse the same Terraform code with different values, such as Region, instance type, or bucket name.                               | variable "instance_type" { default = "t3.micro" }             |
| terraform.tfvars | Provides actual values for the variables declared in variables.tf. Terraform automatically loads values from .tfvars files.                                           | instance_type = "t3.micro"                                    |
| main.tf          | Contains the main infrastructure resources, such as EC2 instances, S3 buckets, VPCs, and security groups.                                                             | resource "aws_instance" "myec2" { ... }                       |
| outputs.tf       | Displays important values after Terraform runs, such as an EC2 public IP, S3 bucket name, or resource ID. Outputs can also be used by other Terraform configurations. | output "instance_ip" { value = aws_instance.myec2.public_ip } |

---

## 2️⃣ Create the Project Folder

```bash
mkdir /infra

cd /infra
```

---

## 3️⃣ provider.tf

```bash
vim provider.tf
```

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

---

## 4️⃣ variables.tf

```bash
vim variables.tf
```

```hcl
# EC2 Instance type
variable "instance_type" {
  description = "Type of EC2 instance"
  type        = string
  default     = "t3.micro"
}

# EC2 AMI ID
variable "ami_id" {
  description = "AMI ID to launch EC2 instance"
  type        = string
  default     = "ami-051a31ab2f4d498f5"  
}

# EC2 Key Pair
variable "key_name" {
  description = "Key pair name for EC2"
  type        = string
}

# Tags
variable "instance_name" {
  description = "Name tag for EC2 instance"
  type        = string
  default     = "Instance1"
}
```

> 💡 `key_name` has **no default**, so its value must be supplied, which is done in `terraform.tfvars` below.

---

## 5️⃣ terraform.tfvars

```bash
vim terraform.tfvars
```

```hcl
# Replace with your actual key pair name
key_name      = "keypair5"
instance_type = "t3.micro"
instance_name = "Instance1"
```

> 🔁 Terraform input variables, so you can reuse the same configuration with different values.

---

## 6️⃣ main.tf

```bash
vim main.tf
```

```hcl
resource "aws_instance" "ec2_instance" {
  ami           = var.ami_id
  instance_type = var.instance_type
  key_name      = var.key_name

  tags = {
    Name = var.instance_name
  }
}
```

---

## 7️⃣ outputs.tf

> 📤 This `outputs.tf` file tells Terraform to display important details about the EC2 instance after it is created: its ID, public IP, and private IP.

```bash
vim outputs.tf
```

```hcl
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.ec2_instance.id
}

output "public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.ec2_instance.public_ip
}

output "private_ip" {
  description = "Private IP of the EC2 instance"
  value       = aws_instance.ec2_instance.private_ip
}
```

---

## 8️⃣ Run Terraform Commands

```bash
terraform init

terraform plan

terraform apply

terraform destroy
```

---

## 9️⃣ Terraform Output Commands

> 🔍 These are Terraform commands that read values saved in Terraform's state after your infrastructure was created.
> `terraform output` shows **all** declared outputs; `terraform output <name>` shows **one** specific output.

```bash
terraform output

terraform output instance_id

terraform output public_ip

terraform output private_ip
```

---

## 🔟 Destroy a Particular Resource

> 🎯 To delete a particular resource.

```bash
terraform destroy -target=aws_instance.myinstance2
```

> ⚠️ The value after `-target=` must match the resource address in your code (`resource_type.resource_name`). For the project above that would be `aws_instance.ec2_instance`; `myinstance2` is just the example address from your notes.

---

## 1️⃣1️⃣ What is Terraform State File Locking Mechanism?

Its inbuilt service advantage of Terraform.

State locking is a mechanism that prevents multiple users from modifying the Terraform state file simultaneously.

Terraform automatically locks the state file to prevent multiple users from modifying it at the same time.

> 🔒 Terraform state file locking is a safety mechanism that prevents two Terraform operations, such as two engineers or CI pipelines running `terraform apply`, from modifying the same state at the same time.
> It acts like a **mutex**: one operation acquires the lock, completes, and releases it; other operations must wait or fail rather than corrupt or overwrite the state.

---

## 1️⃣2️⃣ What is Terraform State File?

Terraform state is a file that keeps track of **what infrastructure Terraform has created**.

Terraform compares **Desired State** (code in the `main.tf` file) with **Current/Actual State** (`terraform.tfstate` file).

Then it calculates what to **create / update / destroy**.

```
  📝 Desired State          🗄️ Current State
   (main.tf code)    vs.   (terraform.tfstate)
          \                      /
           \                    /
            ▼                  ▼
        🧠 Terraform calculates the changes
                    │
                    ▼
        ➕ Create   🔄 Update   ➖ Destroy
```

---

## 1️⃣3️⃣ What Happens Without State?

Without state:

- ❌ Terraform doesn't know what it already created
- ❌ It may try to recreate resources
- ❌ It cannot detect changes

---

## 1️⃣4️⃣ What's Inside terraform.tfstate?

**State file contains:**

- 🆔 Resource IDs (like EC2 instance ID)
- 📋 Resource attributes
- 🔗 Dependencies
- 🏷️ Metadata

**State file may contain:**

- 🔑 Passwords
- 🗝️ DB credentials
- 🤫 Secrets

> 🚨 **So it must be protected.**

---

## 1️⃣5️⃣ Types of Terraform State File

### 1) 💻 Local State (Default)

Stored locally: `terraform.tfstate`

**Problem:**

- ⛔ Not safe for teams
- ⛔ Risk of deletion
- ⛔ No versioning

### 2) ☁️ Remote State (Recommended for Production)

Stored remotely like:

- AWS S3
- Azure Storage
- GCS

**Benefits:**

- ✅ Team collaboration
- ✅ Centralized
- ✅ Locking support
- ✅ Versioning

---

## 1️⃣6️⃣ Create S3 Bucket & DynamoDB Table to Keep tfstate File

| 🧩 Resource | 🏷️ Value |
|------------|---------|
| **S3 bucket name** | `my-terraform-state-bucket-510135` |
| **DynamoDB table name** | `terraform-lock-table` |
| **Primary key** | `LockID` |

> 💡 Create these **before** running `terraform init` with the backend configuration below, because Terraform needs the bucket (and table) to already exist.

---

## 1️⃣7️⃣ Terraform Remote Backend Configuration

> 🌐 It tells Terraform to store its infrastructure state file in Amazon S3 rather than locally, and to use DynamoDB for locking so multiple people/processes cannot run Terraform at the same time.

```bash
vim backend.tf
```

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket-510135"
    key            = "infra/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-lock-table"
    encrypt        = true
  }
}
```

### 📘 Code Explanation

| 🔧 Argument | 📖 Meaning |
|------------|-----------|
| `bucket` | S3 bucket that stores the state file |
| `key` | Path (file name) of the state file inside the bucket |
| `region` | AWS region where the bucket is located |
| `dynamodb_table` | DynamoDB table used for state locking (primary key `LockID`) |
| `encrypt` | Encrypts the state file at rest in S3 |

---

| Part                                        | Meaning                                                                                                                         |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------|
| terraform { ... }                           | Top-level Terraform configuration block.                                                                                        |
| backend "s3"                                | Uses AWS S3 as the remote state backend.                                                                                        |
| bucket = "my-terraform-state-bucket-510135" | The existing S3 bucket where the Terraform state file will be stored.                                                           |
| key = "infra/terraform.tfstate"             | The object path inside that bucket; Terraform will store state at s3://my-terraform-state-bucket-510135/infra/terraform.tfstate.|
| region = "ap-south-1"                       | The AWS region where the S3 bucket—and DynamoDB table, if used—must exist. Here, that is Mumbai (ap-south-1).                   |
| dynamodb_table = "terraform-lock-table"     | The DynamoDB table Terraform uses for state locking and consistency checking.                                                   |
| encrypt = true                              | Enables server-side encryption for the state object stored in S3.                                                               |
---

### ⚠️ Important Caveat

AWS and HashiCorp note that DynamoDB-based locking is **deprecated**; newer Terraform versions can use **S3-native locking** through `use_lockfile = true`.
Your configuration will still work with the DynamoDB approach, but for new setups consider:

```hcl
terraform {
  backend "s3" {
    bucket       = "my-terraform-state-bucket-510135"
    key          = "infra/terraform.tfstate"
    region       = "ap-south-1"
    encrypt      = true
    use_lockfile = true
  }
}
```

---

### ▶️ Initialize and Run Again

```bash
terraform init

terraform plan

terraform apply

terraform destroy
```

---

<div align="center">

### 🗂️ Final Project Layout

```
/infra
├── backend.tf          # Remote state + locking
├── provider.tf         # AWS provider
├── variables.tf        # Input variables
├── terraform.tfvars    # Variable values
├── main.tf             # EC2 resource
└── outputs.tf          # Instance ID, public & private IP
```

Made with ❤️ and ☕ for learning **Infrastructure as Code**

</div>
