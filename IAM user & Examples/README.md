
<div align="center">

# 🔐 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 × 𝐀𝐖𝐒 𝐈𝐀𝐌
### 𝘏𝘢𝘯𝘥𝘴-𝘖𝘯 𝘎𝘶𝘪𝘥𝘦 𝘵𝘰 𝘊𝘳𝘦𝘢𝘵𝘪𝘯𝘨 𝘐𝘈𝘔 𝘜𝘴𝘦𝘳𝘴 𝘢𝘯𝘥 𝘈𝘵𝘵𝘢𝘤𝘩𝘪𝘯𝘨 𝘗𝘰𝘭𝘪𝘤𝘪𝘦𝘴 𝘸𝘪𝘵𝘩 𝘐𝘯𝘧𝘳𝘢𝘴𝘵𝘳𝘶𝘤𝘵𝘶𝘳𝘦 𝘢𝘴 𝘊𝘰𝘥𝘦

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS IAM](https://img.shields.io/badge/AWS-IAM-DD344C?style=for-the-badge&logo=amazonaws&logoColor=white)
![Security](https://img.shields.io/badge/Focus-Access%20Management-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Level](https://img.shields.io/badge/Level-Beginner%20to%20Pro-2E8B57?style=for-the-badge)

*A practical guide to managing identities and permissions on AWS using Terraform* ☁️

</div>

---

## 📑 Table of Contents

| # | Topic | Description |
|:-:|-------|-------------|
| 1️⃣ | [Create IAM User](#1️⃣-create-iam-user) | Basic user creation |
| 2️⃣ | [IAM User with EC2 Access Policy](#2️⃣-create-iam-user-with-ec2-access-policy) | Attach a managed policy |
| 3️⃣ | [IAM User with EC2 & S3 Policies](#3️⃣-create-iam-user-with-ec2-access-policy--s3-bucket-access-policy) | Attach multiple policies |
| 4️⃣ | [Multiple IAM Users with Policies](#4️⃣-create-multiple-iam-users-and-give-ec2-access-policy--s3-bucket-access-policies) | Several users, repeated attachments |
| 5️⃣ | [Key Concepts](#5️⃣-key-concepts-you-should-know) | How the resources fit together |
| 6️⃣ | [Real-Time Examples](#6️⃣-real-time-examples-used) | Groups, `for_each`, custom policies, roles |
| 7️⃣ | [Best Practices](#7️⃣-best-practices-followed-in-companies) | Security checklist |
| 8️⃣ | [Verify & Clean Up](#8️⃣-verify-and-clean-up) | CLI checks and destroy |

---

## 1️⃣ Create IAM User

> 🎯 **Goal:** Create a single IAM user named `raj`.

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_iam_user" "raj_user" {
  name = "raj"
}
```

---

## 2️⃣ Create IAM User with EC2 Access Policy

> 🖥️ **Goal:** Create the user `raj` and give the user EC2 access using an AWS managed policy.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Create Raj user
resource "aws_iam_user" "raj_user" {
  name = "raj"
}

# Attach EC2 Full Access policy to Raj
resource "aws_iam_user_policy_attachment" "raj_ec2_full_access" {
  user       = aws_iam_user.raj_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
}
```

---

## 3️⃣ Create IAM User with EC2 Access Policy & S3 Bucket Access Policy

> 🖥️🪣 **Goal:** Give the same user both EC2 and S3 permissions by attaching two policies.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Create Raj user
resource "aws_iam_user" "raj_user" {
  name = "raj"
}

# Attach EC2 Full Access policy to Raj
resource "aws_iam_user_policy_attachment" "raj_ec2_full_access" {
  user       = aws_iam_user.raj_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
}

# Attach S3 Full Access policy to Raj
resource "aws_iam_user_policy_attachment" "raj_s3_full_access" {
  user       = aws_iam_user.raj_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}
```

---

## 4️⃣ Create Multiple IAM Users and Give EC2 Access Policy & S3 Bucket Access Policies

> 👥 **Goal:** Create three users (`raj`, `akshay`, `darshan`) and attach EC2 and S3 policies to each of them.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Create Raj user
resource "aws_iam_user" "raj_user" {
  name = "raj"
}

# Create Akshay user
resource "aws_iam_user" "akshay_user" {
  name = "akshay"
}

# Create Darshan user
resource "aws_iam_user" "darshan_user" {
  name = "darshan"
}

# Attach EC2 Full Access to raj
resource "aws_iam_user_policy_attachment" "raj_ec2_full_access" {
  user       = aws_iam_user.raj_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
}

# Attach S3 Full Access to raj
resource "aws_iam_user_policy_attachment" "raj_s3_full_access" {
  user       = aws_iam_user.raj_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}

# Attach EC2 Full Access to akshay
resource "aws_iam_user_policy_attachment" "akshay_ec2_full_access" {
  user       = aws_iam_user.akshay_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
}

# Attach S3 Full Access to akshay
resource "aws_iam_user_policy_attachment" "akshay_s3_full_access" {
  user       = aws_iam_user.akshay_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}

# Attach EC2 Full Access to darshan
resource "aws_iam_user_policy_attachment" "darshan_ec2_full_access" {
  user       = aws_iam_user.darshan_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
}

# Attach S3 Full Access to darshan
resource "aws_iam_user_policy_attachment" "darshan_s3_full_access" {
  user       = aws_iam_user.darshan_user.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}
```

---

## 5️⃣ Key Concepts You Should Know

> 💡 *Additional notes to help you understand what the code above is doing.*

| 🔑 Concept | 📖 Explanation |
|-----------|---------------|
| **`aws_iam_user`** | Creates the IAM identity (the user). A new user has **no permissions** and **no login** until you grant them. |
| **`aws_iam_user_policy_attachment`** | Attaches **one managed policy** to **one user**. Each attachment is its own resource, which is why the multi-user example gets long. |
| **`policy_arn`** | The unique ID of a policy. Policies owned by AWS use `arn:aws:iam::aws:policy/...`, and your own policies use your account ID. |
| **Reference `aws_iam_user.raj_user.name`** | Terraform reads the user's name from the user resource, so it creates the user **first** and the attachment **second** (implicit dependency). |
| **IAM is global** | IAM is not tied to a region. The `region = "ap-south-1"` in the provider block is required by the provider, but the users and policies you create are available across the whole account. |
| **Unique names** | IAM user names must be **unique within the AWS account**, otherwise `terraform apply` fails with an `EntityAlreadyExists` error. |

> ⚠️ **Heads up:** `AmazonEC2FullAccess` and `AmazonS3FullAccess` are very broad. They are great for **learning and labs**, but production teams usually replace them with narrower policies (see section 6️⃣).

---

## 6️⃣ Real-Time Examples Used in Companies

> 🏢 *The examples in this section are additional to your original notes. They show how the same ideas are used in real teams. Bucket names, group names and account details are placeholders, so replace them with your own.*

### 🧩 Example A: Use IAM Groups Instead of Attaching Policies to Each User

**Scenario:** A company has 20 developers who all need the same access. Attaching policies to every user (like example 4️⃣) means 40 attachment resources. Companies create a **group**, attach the policies **once**, and add users to it.

```hcl
provider "aws" {
  region = "ap-south-1"
}

# Group for all developers
resource "aws_iam_group" "developers" {
  name = "developers"
}

# Attach policies ONCE to the group
resource "aws_iam_group_policy_attachment" "developers_ec2" {
  group      = aws_iam_group.developers.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
}

resource "aws_iam_group_policy_attachment" "developers_s3" {
  group      = aws_iam_group.developers.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}

# Create the users
resource "aws_iam_user" "team" {
  for_each = toset(["raj", "akshay", "darshan"])
  name     = each.value

  tags = {
    Team = "backend"
  }
}

# Add every user to the group
resource "aws_iam_user_group_membership" "team" {
  for_each = aws_iam_user.team
  user     = each.value.name
  groups   = [aws_iam_group.developers.name]
}
```

✅ **Why companies do this:** When a developer joins, you add one name to the list. When they leave, you remove it. No policy attachments to touch.

---

### 🔒 Example B: Least-Privilege Custom Policy

**Scenario:** A reporting analyst only needs to **read** files from one bucket. Giving `AmazonS3FullAccess` would let them delete every bucket in the account, so companies write a custom policy.

```hcl
resource "aws_iam_policy" "s3_read_reports" {
  name        = "S3ReadOnlyReportsBucket"
  description = "Read-only access to the company reports bucket"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:ListBucket"
        ]
        Resource = "arn:aws:s3:::company-reports-bucket"
      },
      {
        Effect = "Allow"
        Action = [
          "s3:GetObject"
        ]
        Resource = "arn:aws:s3:::company-reports-bucket/*"
      }
    ]
  })
}

resource "aws_iam_user" "analyst" {
  name = "priya"
}

resource "aws_iam_user_policy_attachment" "analyst_reports" {
  user       = aws_iam_user.analyst.name
  policy_arn = aws_iam_policy.s3_read_reports.arn
}
```

✅ **Why companies do this:** If the analyst's credentials are ever leaked, the damage is limited to reading one bucket.

---

### 🧑‍💻 Example C: Console Login with Forced Password Reset

**Scenario:** On onboarding day, IT creates a login for a new employee. The employee must change the password on first sign-in.

```hcl
resource "aws_iam_user_login_profile" "raj_console" {
  user                    = aws_iam_user.raj_user.name
  password_reset_required = true
}
```

> ⚠️ The generated initial password is stored in the **Terraform state file**. Companies keep state in an encrypted, access-restricted remote backend (for example an S3 bucket with encryption and restricted access) and never commit it to Git.

---

### 🤖 Example D: Use an IAM Role for Applications, Not Users

**Scenario:** A web application running on EC2 needs to upload files to S3. Companies **do not** create an IAM user and paste access keys into the server. They attach an **IAM role** to the instance.

```hcl
# Role that EC2 is allowed to assume
resource "aws_iam_role" "app_role" {
  name = "app-server-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect    = "Allow"
      Principal = { Service = "ec2.amazonaws.com" }
      Action    = "sts:AssumeRole"
    }]
  })
}

# Give the role S3 access
resource "aws_iam_role_policy_attachment" "app_s3" {
  role       = aws_iam_role.app_role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}

# Instance profile is what you attach to an EC2 instance
resource "aws_iam_instance_profile" "app_profile" {
  name = "app-server-profile"
  role = aws_iam_role.app_role.name
}
```

Then reference it in your EC2 resource (from the EC2 guide) with:

```hcl
iam_instance_profile = aws_iam_instance_profile.app_profile.name
```

✅ **Why companies do this:** The credentials are temporary and rotated automatically, so there are no long-lived keys to leak.

---

### 📊 Quick Comparison

| Approach | Best For | Maintenance |
|----------|----------|:-----------:|
| Policy attached to each user (examples 2️⃣–4️⃣) | Learning, labs, one-off users | 😓 High |
| IAM Group + policies (Example A) | Teams with the same access needs | 😊 Low |
| Custom least-privilege policy (Example B) | Sensitive data, compliance requirements | 😊 Low |
| IAM Role (Example D) | Applications, servers, CI/CD pipelines | 🌟 Lowest |

---

## 7️⃣ Best Practices Followed in Companies

- ✅ **Least privilege:** Grant only the permissions a person actually needs, and avoid `*FullAccess` policies in production.
- ✅ **Use groups, not individual attachments:** Easier to audit and onboard or offboard people.
- ✅ **Enable MFA:** Require multi-factor authentication for every human user, especially anyone with console access.
- ✅ **Avoid long-lived access keys:** Prefer IAM roles for applications and AWS IAM Identity Center (SSO) for people. If keys are unavoidable, rotate them regularly.
- ✅ **Never hard-code credentials in `.tf` files or Git:** Use environment variables, the AWS CLI profile, or a CI/CD secrets store.
- ✅ **Protect the Terraform state file:** It can contain sensitive values. Use an encrypted remote backend with restricted access.
- ✅ **Tag your users:** Tags such as `Team`, `Owner` and `Environment` make audits and cost reviews easier.
- ✅ **Review regularly:** Use IAM Access Analyzer and credential reports to find unused users, keys and permissions.
- ✅ **Don't use the root account for daily work:** Keep it locked away with MFA.
- ✅ **Keep code DRY:** Once you repeat the same block three times, as in example 4️⃣, switch to `for_each` or groups.

---

## 8️⃣ Verify and Clean Up

### 🔍 Check what Terraform created

```bash
# List all IAM users
aws iam list-users

# See which policies are attached to a user
aws iam list-attached-user-policies --user-name raj
```

### 🧹 Remove everything when you are done

```bash
terraform destroy
```

> ⚠️ Always run `terraform plan` first and read the output carefully. In a real company account, an unreviewed change to IAM can lock people out or open up access unintentionally.

---

<div align="center">

### ⚙️ Quick Command Reference

```bash
terraform init      # Initialize the working directory
terraform fmt       # Format your code
terraform validate  # Check the configuration for errors
terraform plan      # Preview the changes
terraform apply     # Create the infrastructure
terraform destroy   # Tear everything down
```

✨Made with ❤️ and ☕ for learning (IaC) **Infrastructure as Code**🌟

</div>
