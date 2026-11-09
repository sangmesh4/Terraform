<div align="center">

# ☁️ Terraform from Scratch
### A Practical Guide to Creating an EC2 Instance, an S3 Bucket, and an IAM User

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20S3%20%7C%20IAM-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)

*Practical, example-driven Terraform notes for learning Infrastructure as Code on AWS*

</div>

---

## 📚 What's Inside

| # | Guide | What You'll Learn |
|:-:|-------|-------------------|
| 1️⃣ | [EC2 with Terraform](./README.md) | `count`, multi-region providers, `user_data` with Apache |
| 2️⃣ | [S3 with Terraform](./README-S3.md) | `count`, `for_each`, multi-region buckets, uploading objects |
| 3️⃣ | [IAM with Terraform](./README-IAM.md) | Users, policy attachments, groups, roles, least-privilege tips |
| 4️⃣ | [Project Structure & State](./README-Terraform-Project.md) | `.tf` file layout, variables, outputs, state locking, S3 remote backend |

## 🚀 Quick Start

```bash
terraform init      # Initialize the working directory
terraform plan      # Preview the changes
terraform apply     # Create the infrastructure
terraform destroy   # Tear everything down
```

## ✅ Prerequisites

- Terraform installed
- AWS CLI configured with valid credentials
- An existing EC2 key pair (for the EC2 examples)

> ⚠️ Replace the AMI IDs, key pair names and bucket names with your own. Bucket names must be globally unique.
