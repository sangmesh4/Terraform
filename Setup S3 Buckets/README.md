
<div align="center">

# 🪣 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 × 𝐀𝐖𝐒 𝐒𝟑
### 𝘏𝘢𝘯𝘥𝘴-𝘖𝘯 𝘌𝘹𝘢𝘮𝘱𝘭𝘦𝘴 𝘧𝘰𝘳 𝘊𝘳𝘦𝘢𝘵𝘪𝘯𝘨 𝘚3 𝘉𝘶𝘤𝘬𝘦𝘵𝘴 𝘢𝘯𝘥 𝘖𝘣𝘫𝘦𝘤𝘵𝘴 𝘸𝘪𝘵𝘩 𝘐𝘯𝘧𝘳𝘢𝘴𝘵𝘳𝘶𝘤𝘵𝘶𝘳𝘦 𝘢𝘴 𝘊𝘰𝘥𝘦

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS S3](https://img.shields.io/badge/AWS-S3-569A31?style=for-the-badge&logo=amazons3&logoColor=white)
![Region](https://img.shields.io/badge/Region-ap--south--1-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-Machine-FCC624?style=for-the-badge&logo=linux&logoColor=black)

*A practical, example-driven guide to creating S3 buckets using Terraform* ☁️

</div>

---

## 📑 Table of Contents

| # | Topic | Description |
|:-:|-------|-------------|
| 1️⃣ | [Create S3 Bucket](#1️⃣-create-s3-bucket) | Single bucket |
| 2️⃣ | [Create Two S3 Buckets](#2️⃣-create-two-s3-buckets) | Two separate resources |
| 3️⃣ | [Two S3 Buckets with Index Numbers](#3️⃣-create-two-s3-buckets-with-index-numbers) | `count` and `count.index` |
| 4️⃣ | [Two S3 Buckets with Same One Resource](#4️⃣-create-two-s3-buckets-with-same-one-resource) | `for_each` |
| 5️⃣ | [Two S3 Buckets in Different Regions](#5️⃣-create-two-s3-buckets-in-different-regions) | Multiple providers with `alias` |
| 6️⃣ | [Create Bucket and Upload File](#6️⃣-create-s3-bucket-and-upload-file-from-local-system-object-to-s3-bucket) | `aws_s3_object` |
| 7️⃣ | [Difference Between key & source](#7️⃣-difference-between-key--source) | Object arguments explained |

---

## 1️⃣ Create S3 Bucket

> 🎯 **Goal:** Create a single S3 bucket in the Mumbai region.

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "celeius51012345"
}
```

---

## 2️⃣ Create Two S3 Buckets

> 🪣🪣 **Goal:** Create two buckets using two separate resource blocks.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Bucket 1
resource "aws_s3_bucket" "sangam_bucket" {
  bucket = "sangam06112026"
}

# Bucket 2
resource "aws_s3_bucket" "sonu_bucket" {
  bucket = "sonu6112026"
}
```

---

## 3️⃣ Create Two S3 Buckets with Index Numbers

> 🔢 **Goal:** Create two numbered buckets using the `count` meta-argument.

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "my_buckets" {
  count  = 2
  bucket = "sanagmraj-terraform-bucket${count.index + 1}"
}
```

---

## 4️⃣ Create Two S3 Buckets with Same One Resource

> ♻️ **Goal:** Create multiple buckets from a single resource block using `for_each`.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Define multiple buckets using for_each
resource "aws_s3_bucket" "my_buckets" {
  for_each = {
    sonu = "sonu5101234"
    sangam= "sangam51012345"
  }
  bucket = each.value
}
```

---

## 5️⃣ Create Two S3 Buckets in Different Regions

> 🌍 **Goal:** Create one bucket in **Mumbai** and another in **Ohio** using a default provider plus an aliased provider.

```hcl
# Default Provider (Mumbai)
provider "aws" {
  region = "ap-south-1"
}

# Second Provider (ohio)
provider "aws" {
  alias  = "ohio"
  region = "us-east-2"
}

# Bucket 1 - Mumbai
resource "aws_s3_bucket" "sanket_bucket" {
  bucket = "sanket51012345"
}

# Bucket 2 - ohio
resource "aws_s3_bucket" "sangeet_bucket" {
  provider = aws.ohio
  bucket   = "sangeet51012345"
}
```

---

## 6️⃣ Create S3 Bucket and Upload File from Local System (Object) to S3 Bucket

> 📤 **Goal:** Create a bucket and upload a local file into it as an S3 object.

> ⚠️ Make sure the file and file path are appropriate on the Linux machine and match the source mentioned below.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Create S3 Bucket
resource "aws_s3_bucket" "my_bucket" {
  bucket = "lekharaj14141"
}

# Upload index.html to the bucket
resource "aws_s3_object" "index_file" {
  bucket = aws_s3_bucket.my_bucket.id             (keep same name as per resource mentioned above during bucket creation , here is my_bucket )
  key    = "file1"                               # name of the object in S3
  source = "/tmp/file1"                 # local file path (Linux machine file path) ; Verify that the file exists at the correct path on the Linux machine, as mentioned in the source.
}
```

### 📘 What This Code Does

This code creates an S3 bucket in the **Mumbai AWS Region** and uploads a local file called `index.html` into that bucket. It is commonly used to prepare a simple static website or store a file in S3.
| Code                                     | What it does                                                                                                                                                    |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| provider "aws" { region = "ap-south-1" } | Tells Terraform to use AWS and create resources in the Asia Pacific (Mumbai) Region.                                                                            |
| resource "aws_s3_bucket" "my_bucket"     | Creates an S3 bucket. my_bucket is Terraform’s internal resource name, used to refer to this bucket elsewhere in the configuration.                             |
| bucket = "lekharaj14141"                 | Sets the actual S3 bucket name to lekharaj14141. S3 bucket names must be globally unique and lowercase.                                                         |
| resource "aws_s3_object" "index_file"    | Uploads a file into the S3 bucket. index_file is Terraform’s internal name for this uploaded object.                                                            |
| bucket = aws_s3_bucket.my_bucket.id      | Tells Terraform to upload the object into the bucket created above. Here, my_bucket must match the resource name used in resource "aws_s3_bucket" "my_bucket".  |
| key = "file1"                            | Sets the object name/path inside the S3 bucket. After upload, the object will be stored as file1 in the bucket.                                                 |
| source = "/tmp/file1"                    | Specifies the local Linux file that Terraform will upload. Terraform reads the file from /tmp/file1 on the machine where Terraform is running.                  |
---

## 7️⃣ Difference Between key & source

| 🏷️ Argument | 📖 Meaning |
|:-----------:|-----------|
| **`key`** | This is the **name of the object in S3**, how it will appear inside the bucket. It can include a path if we want folders in S3. |
| **`source`** | This is the **local file path** on our machine or Terraform working directory that will be uploaded to S3. |

---

<div align="center">

### ⚙️ Quick Command Reference

```bash
terraform init      # Initialize the working directory
terraform plan      # Preview the changes
terraform apply     # Create the infrastructure
terraform destroy   # Tear everything down
```

✨Made with ❤️ and ☕ for learning (IaC) **Infrastructure as Code**🌟

</div>
