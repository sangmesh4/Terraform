<div align="center">

# 🚀 Terraform on AWS
### *Infrastructure as Code — Getting Started Guide*

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?logo=amazonaws&logoColor=white)
![HCL](https://img.shields.io/badge/Language-HCL-blue)
![Go](https://img.shields.io/badge/Written%20in-Go-00ADD8?logo=go&logoColor=white)

*Provision and manage cloud infrastructure with simple, declarative code.*

</div>

---

## 📑 Table of Contents

| # | Section |
|---|---------|
| 1 | [📘 Introduction to Terraform and IaC](#1--introduction-to-terraform-and-iac) — *What is IaC? · What is Terraform? · Why use Terraform?* |
| 2 | [🏢 About HashiCorp](#2--about-hashicorp) |
| 3 | [⚙️ Terraform Installation](#3-️-terraform-installation) |
| 4 | [🔐 AWS Setup (IAM User & CLI)](#4--aws-setup-iam-user--cli) |
| 5 | [📝 Sample Code: Create an EC2 Instance](#5--sample-code-create-an-ec2-instance) |
| 6 | [▶️ Running Terraform for the First Time](#6-️-running-terraform-for-the-first-time) |
| 7 | [🔍 Code Breakdown](#7--code-breakdown) |
| 8 | [🔄 Terraform Workflow Commands](#8--terraform-workflow-commands) |
| 9 | [🗂️ Terraform State Files](#9-️-terraform-state-files) |

---

## 1. 📘 Introduction to Terraform and IaC

### 🧩 1.1 What is Infrastructure as Code (IaC)?

**Infrastructure as Code (IaC)** means managing and setting up your infrastructure **using code instead of doing it manually**.

Just like application code, this infrastructure code is stored in **version control systems (VCS)** like **Git**, so every change can be tracked easily. IaC helps:

- 🤖 **Automate** repetitive tasks
- 🎯 Keep your setup **consistent**
- 🛡️ **Reduce mistakes** by defining everything in clear, machine-readable files

### 🌍 1.2 What is Terraform?

**Terraform** is an **IaC tool** that helps you **create and manage infrastructure** on different cloud platforms and even on-premises.

- It uses simple configuration files written in **HCL** (HashiCorp Configuration Language) or **JSON** to describe what your setup should look like.
- Terraform then **builds and manages those resources automatically**, making your infrastructure consistent and easy to reproduce.
- Its **plugin system** supports many providers, so you can work with different services all in one tool.

| Property | Detail |
|----------|--------|
| 🗓️ **Launched** | 2014 by HashiCorp |
| 💻 **Written in** | Go (Golang) |
| 📄 **Config files** | `.tf` files |
| 🔤 **Config language** | HashiCorp Configuration Language (**HCL**) |

### ✨ 1.3 Why use Terraform? (Benefits)

Terraform makes managing infrastructure **simple, consistent, and automated**. Instead of setting up servers manually, you write code to describe what you want — and Terraform handles the rest.

It works with **multiple cloud providers** and even **on-premises systems**, making it great for any environment. Its clean syntax, strong automation, and version control support help teams **work faster and avoid mistakes**.

**🔑 Key Benefits:**

| # | Benefit | Description |
|:-:|---------|-------------|
| 1️⃣ | 🎯 **Consistency** | Infrastructure as code means every setup is repeatable and version-controlled. |
| 2️⃣ | ⚡ **Automation** | Terraform automates the entire lifecycle — from creating to destroying resources. |
| 3️⃣ | 🛡️ **Reliability** | Changes are reviewed before applying, reducing human error. |
| 4️⃣ | ♻️ **Reusable Modules** | Write once, reuse anywhere — saves time and keeps infrastructure organized. |

---

## 2. 🏢 About HashiCorp

**HashiCorp** is an American software company that creates tools for **infrastructure automation** and **DevOps**.

| Property | Detail |
|----------|--------|
| 📅 **Founded** | 2012 |
| 👤 **Founders** | Mitchell Hashimoto and Armon Dadgar |

---

## 3. ⚙️ Terraform Installation

### 🔧 Steps to install Terraform (Amazon Linux)

```bash
yum install -y yum-utils shadow-utils

yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

yum install terraform
```
<img width="822" height="337" alt="image" src="https://github.com/user-attachments/assets/173df92d-ad58-4aae-ba97-df5f95d98e67" />



### ✅ Check the installed version

Any of the following commands works:

```bash
terraform --version
terraform -v
terraform version
```

📤 **Output:**

```text
Terraform v1.17.1
on linux_amd64
```

---

## 4. 🔐 AWS Setup (IAM User & CLI)

> 📌 **Note:** The AWS setup steps below require an IAM user with the appropriate policy to work with Terraform.

### 👤 4.1 Create an IAM user

Create an IAM user to work with the AWS CLI and Terraform, then attach the required IAM policies to that user.

> ✅ When creating the user, make sure they are granted the following policies:
> - `AmazonEC2FullAccess`
> - `AmazonS3FullAccess`
> - `IAMFullAccess`
<img width="1034" height="583" alt="image" src="https://github.com/user-attachments/assets/d01549bd-b273-4098-b6ff-a1f06ec11e94" />


### 🔑 4.2 Get the access keys

Navigate to: **IAM → Users → user1 → Security credentials → Create access key**

```text
Access key        : <YOUR_ACCESS_KEY_ID>
Secret access key : <YOUR_SECRET_ACCESS_KEY>
```

> 🚨 **Security warning:** Never commit access keys to Git, README files, or share them publicly.
> If keys are ever exposed, **deactivate and rotate them immediately** in the IAM console.

### 💻 4.3 Configure the AWS CLI (Linux terminal)

```bash
aws configure
```
<img width="822" height="159" alt="image" src="https://github.com/user-attachments/assets/c88a1e0e-8574-4931-832d-762da4eb9ee5" />

You will be prompted for the access key, secret key, default region (e.g. `ap-south-1`), and output format.

### ℹ️ 4.4 AWS CLI availability by OS

| OS | AWS CLI |
|----|---------|
| 🟠 **Amazon Linux** (incl. Amazon Linux 2 & 2023) | Pre-installed by default — built specifically for the AWS cloud ecosystem |
| 🟣 **Ubuntu Linux** | Not included — a general-purpose distribution by Canonical, so you must install it manually |

---

## 5. 📝 Sample Code: Create an EC2 Instance

After successfully configuring AWS, let's set up sample code to create an EC2 instance and understand the Terraform workflow.

### 📁 Create a working directory

```bash
mkdir /infra
cd /infra          # any separate working directory you can create
```

### ✏️ Create `main.tf` (creates 1 instance)

```bash
vim main.tf        # Indentation matters in the file
```

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "myec2" {
  ami           = "ami-0ff5003538b60d5ec"
  instance_type = "t3.micro"
  key_name      = "instance1-keypair"

  tags = {
    Name = "instance1"
  }
}
```

💡 **What does this code do?**
It tells AWS to create **one EC2 virtual machine** in the **Mumbai region (`ap-south-1`)**, using a **specific AMI**, a **small instance type**, and an **existing SSH key pair**.

---

## 6. ▶️ Running Terraform for the First Time

> ⚠️ **Checklist before running:**
> - ✔️ Region is correct
> - ✔️ AMI ID is correct
> - ✔️ Instance type is correct
> - ✔️ Key pair name is correct
> - ✔️ Key pair is created in the appropriate region

```bash
terraform init      # First run
terraform apply
```

After a successful run, Terraform creates a **`terraform.tfstate`** file in the same `infra` directory. If you run apply again on an existing setup, it will also create **`terraform.tfstate.backup`**.

> 📝 It is **not mandatory** to name the file `main.tf` in Terraform.
> Terraform automatically loads **all files with the `.tf` extension** in the working directory.

<img width="578" height="112" alt="image" src="https://github.com/user-attachments/assets/e5575c8c-9855-49f7-a0a5-8e18ed089b41" />

---

## 7. 🔍 Code Breakdown

Let's break down the code step by step.
| Code                            | What it does                                                                                                        |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| provider "aws" { ... }          | Tells Terraform to use the AWS provider to create and manage AWS resources.                                         |
| region = "ap-south-1"           | Sets the AWS Region to Asia Pacific (Mumbai), where the EC2 instance will be created.                               |
| resource "aws_instance" "myec2" | Defines an EC2 virtual machine resource. myec2 is Terraform’s internal name for it, not the AWS instance name.      |
| ami = "ami-0ff5003538b60d5ec"   | Specifies the Amazon Machine Image (AMI), which determines the operating system and base software for the instance. |
| instance_type = "t3.micro"      | Sets the EC2 instance size and capacity, including CPU, memory, and network performance.                            |
| key_name = "instance1-keypair"  | Uses an existing EC2 key pair for SSH access. The key pair must already exist in AWS.                               |
| tags = { Name = "instance1" }   | Adds a tag to the EC2 instance so it appears as instance1 in the AWS Console.                                       |
| Name = "instance1"              | The display name/tag for identifying the EC2 instance; it does not change the instance’s functionality.             |

### 🧱 7.1 `aws_instance` — Resource Type

- This is the **resource type**
- It tells Terraform **what to create**

**Examples of resource types:**

| Resource Type | Creates |
|---------------|---------|
| `aws_instance` | EC2 instance |
| `aws_s3_bucket` | S3 bucket |
| `aws_iam_user` | IAM user |

### 🏷️ 7.2 `myec2` — Local (Logical) Name

- This is the Terraform **local name (logical name)**
- Used **only inside Terraform code**
- **AWS will not see this name**

### 📌 7.3 Important Points

- ✅ An EC2 instance is still created successfully **even if we don't define tags** — but no `Name` tag will appear in the AWS Console.
- ✅ The **provider block must be declared before** any resources that use it.
- ✅ Terraform needs to know which **cloud / region / account** to use before creating resources.
- ❌ We cannot put a resource before the provider, because Terraform wouldn't know which provider to use.
- 🔀 We **can change the order of arguments** inside the resource block — Terraform doesn't require a specific order.

---

## 8. 🔄 Terraform Workflow Commands

Terraform workflow commands: **initialize** the project, **preview** changes, **create/update** infrastructure, and **delete** infrastructure. Run them from the directory containing your `.tf` files.

| Step | Command | Purpose |
|:----:|---------|---------|
| 1️⃣ | `terraform init` | Initializes providers and backend |
| 2️⃣ | `terraform plan` | Shows the execution plan |
| 3️⃣ | `terraform apply` | Creates infrastructure |
| 4️⃣ | `terraform destroy` | Deletes infrastructure |

```text
init  ➜  plan  ➜  apply  ➜  destroy
```
| Command           | What it does                                                                                                                             | Changes real infrastructure? |
| ----------------- | -----------------------------------------------------------------------------------------------------------------------------------------| ---------------------------- |
| terraform init    | Prepares the working directory: downloads providers/modules, configures the backend, and creates/updates .terraform.lock.hcl. developer. | No                           |
| terraform plan    | Compares your configuration with current state and shows what would be created, changed, or destroyed. developer.                        | No                           |
| terraform apply   | Executes the planned changes through the provider API—for example, creates or updates an AWS EC2 instance. developer.                    | Yes                          |
| terraform destroy | Plans and applies deletion of all resources managed by the current workspace/configuration. developer.                                   | Yes—deletes resources        |


---

## 9. 🗂️ Terraform State Files

### 📄 9.1 `terraform.tfstate`

A **state file** where Terraform stores the **real infrastructure details** that it has created.
Terraform remembers what it created using the `tfstate` file.

| File | Description |
|------|-------------|
| `terraform.tfstate` | Current local state snapshot |
| `terraform.tfstate.backup` | Previous state snapshot |

### 💾 9.2 `terraform.tfstate.backup`

Stores the **previous version** of the Terraform state file. It helps in **recovery** if the current state gets corrupted or modified incorrectly.

### 🔁 9.3 What happens when we run `terraform apply`?

1. Terraform **updates** `terraform.tfstate`
2. Before updating, it **creates a backup** of the previous state
3. The backup is stored as `terraform.tfstate.backup`

---

<div align="center">

### ⭐ Happy Terraforming! ⭐

👨🏻‍💻👉*Plan before you apply. Destroy what you don't need.*🔎👨🏻‍💻

</div>
