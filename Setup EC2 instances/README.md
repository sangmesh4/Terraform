
<div align="center">

# 🚀 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 × 𝐀𝐖𝐒 𝐄𝐂𝟐
### 𝘏𝘢𝘯𝘥𝘴-𝘖𝘯 𝘌𝘹𝘢𝘮𝘱𝘭𝘦𝘴 𝘧𝘰𝘳 𝘗𝘳𝘰𝘷𝘪𝘴𝘪𝘰𝘯𝘪𝘯𝘨 𝘌𝘊2 𝘐𝘯𝘴𝘵𝘢𝘯𝘤𝘦𝘴 𝘸𝘪𝘵𝘩 𝘐𝘯𝘧𝘳𝘢𝘴𝘵𝘳𝘶𝘤𝘵𝘶𝘳𝘦 𝘢𝘴 𝘊𝘰𝘥𝘦

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Region](https://img.shields.io/badge/Region-ap--south--1-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Apache](https://img.shields.io/badge/Apache-httpd-D22128?style=for-the-badge&logo=apache&logoColor=white)

*A practical, example-driven guide to creating EC2 instances using Terraform* ☁️

</div>

---

## 📑 Table of Contents

| # | Topic | Description |
|:-:|-------|-------------|
| 1️⃣ | [Create 2 Instances Without Name](#1️⃣-create-2-instances-without-name) | Using `count` |
| 2️⃣ | [Create 2 Instances in Different Regions](#2️⃣-create-2-instances-in-different-regions) | Multiple providers with `alias` |
| 3️⃣ | [Create 3 Instances with Index Numbers](#3️⃣-create-3-instances-with-index-numbers) | `count.index` naming |
| 4️⃣ | [Install Apache & Setup index.html](#4️⃣-create-instance-and-install-apache-and-setup-indexhtml-file) | `user_data` with heredoc |
| 5️⃣ | [One-Line user_data Approach](#5️⃣-another-way-to-setup-indexhtml-with-one-line-user_data) | Single-string `user_data` |
| 6️⃣ | [Important Points](#6️⃣-some-important-points) | Security group behavior |

---

## 1️⃣ Create 2 Instances Without Name

> 🎯 **Goal:** Launch two identical EC2 instances using the `count` meta-argument (no `Name` tag).

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "myec2" {
  count         = 2 
  ami           = "ami-0ff5003538b60d5ec"
  instance_type = "t3.micro"
  key_name      = "instance1-keypair"
}
```

---

## 2️⃣ Create 2 Instances in Different Regions

> 🌍 **Goal:** Deploy one instance in **Mumbai** and another in **Ohio** using a default provider plus an aliased provider.

```hcl
# Default Provider (Mumbai)
provider "aws" {
  region = "ap-south-1"
}

# Second Provider (Ohio)
provider "aws" {
  alias  = "ohio"
  region = "us-east-2"
}

# Instance 1 - Mumbai
resource "aws_instance" "instance1" {
  ami           = "ami-0ff5003538b60d5ec"  # Mumbai AMI
  instance_type = "t3.micro"
  key_name      = "instance1-keypair"

  tags = {
    Name = "instance1"
  }
}

# Instance 2 - Ohio
resource "aws_instance" "instance2" {
  provider      = aws.ohio
  ami           = "ami-01766fbdbd31a472f"  # Ohio AMI (must match Ohio region)
  instance_type = "t3.micro"
  key_name      = "instance2-keypair"

  tags = {
    Name = "instance2"
  }
}
```

---

## 3️⃣ Create 3 Instances with Index Numbers

> 🔢 **Goal:** Launch three instances named `instance1`, `instance2`, `instance3`.

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "myec2" {
  count         = 3
  ami           = "ami-0ff5003538b60d5ec"
  instance_type = "t3.micro"
  key_name      = "instance1-keypair"

  tags = {
    Name = "instance${count.index + 1}"
  }
}
```

### 💡 How it Works

- `count = 3` creates **3** EC2 instances
- `count.index` starts from **0**

---

## 4️⃣ Create Instance and Install Apache and Setup index.html File

> 🌐 **Goal:** Launch an instance that automatically installs Apache (httpd) and serves a custom web page.

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0ff5003538b60d5ec"
  instance_type = "t3.micro"
  key_name      = "instance1-keypair"

  # User data to install Apache and setup index.html
  user_data = <<-EOF
              #!/bin/bash
              yum update -y
              yum install -y httpd
              systemctl start httpd
              systemctl enable httpd
              echo "<h1>Welcome to terraform Website</h1>" > /var/www/html/index.html
              EOF

  tags = {
    Name = "Instance-1"
  }
}
```

> ⚠️ **Note:** After creation of the instance, open the Security Group **inbound port 80**, and check with the `public_ip` in your browser.

### 📘 What is `<<-EOF … EOF`?

- `<<-EOF … EOF` is called a **heredoc** (here-document)
- It allows us to write **multi-line strings** in Terraform (or Bash, or other languages)
- Everything between `<<-EOF` and `EOF` becomes the string content
- Without heredoc, multi-line content would be hard to read:

```hcl
user_data = "#!/bin/bash\nyum update -y\nyum install -y httpd\nsystemctl start httpd\n..."
```

---

## 5️⃣ Another Way to Setup index.html with One-Line user_data

> ⚡ **Goal:** Achieve the same result with `user_data` written as a single-line string.

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0317b0f0a0144b137"
  instance_type = "t2.micro"
  key_name      = "keypair5"
  user_data = "#!/bin/bash\nyum update -y\nyum install -y httpd\nsystemctl start httpd\nsystemctl enable httpd\necho \"<h1>Welcome to Terraform Website</h1>\" > /var/www/html/index.html"
  
  tags = {
    Name = "Instance-1"
  }
}
```

---

## 6️⃣ Some Important Points

> 🔐 **Security Group behavior to keep in mind**

- 🔹 When we create an EC2 instance **without specifying a security group**, AWS automatically assigns the instance to the **default security group** of the VPC.

- 🔹 In some AWS regions, the AMI we are using (Amazon Linux 2) comes with a default security group for **"EC2-Classic"** in older accounts.

- 🔹 In some AWS regions, some AMIs automatically suggest or create a security group with **HTTP (80)** open when launched via console, but **Terraform itself does not open port 80**.

> 🚨 Our `user_data` only installs `httpd` — it does **not** modify the SG.
> **Terraform does not automatically open port 80.**

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
