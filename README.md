# ☁️ Cloud-Based Inventory Management System

A production-ready, cloud-native inventory management web application built for small businesses to track products, monitor warehouse stock, process point-of-sale transactions, and receive automated low-stock warnings. Automated end-to-end deployment on **Amazon Web Services (AWS)** using **Terraform (Infrastructure as Code)**.

---

## 🏗️ System Architecture

```
                                  [ INTERNET ]
                                        │
                                        ▼ (Port 80 / HTTP)
┌───────────────────────────────────────┼────────────────────────────────────────┐
│ AWS VPC (10.0.0.0/16)                 │                                        │
│                                       ▼                                        │
│  ┌──────────────────────────────────────────────────────────────────────────┐  │
│  │ Public Subnet (10.0.1.0/24)                                              │  │
│  │  ┌────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ AWS EC2 (t3.micro - Amazon Linux 2023)                             │  │  │
│  │  │  • Flask Application + Gunicorn Systemd Service                    │  │  │
│  │  │  • Automated Database Schema Migrations                            │  │  │
│  │  │  • Least-Privilege IAM Instance Profile                            │  │  │
│  │  └───────────────────┬───────────────────────────────┬────────────────┘  │  │
│  └──────────────────────┼───────────────────────────────┼───────────────────┘  │
│                         │ (MySQL Port 3306)             │ (HTTPS IAM)          │
│                         ▼                               ▼                      │
│  ┌──────────────────────────────────────────┐  ┌────────────────────────────┐  │
│  │ Private Subnets (10.0.10.0/24 & .11.0/24)│  │ AWS S3 Bucket              │  │
│  │  ┌────────────────────────────────────┐  │  │  • Product Media & Images  │  │
│  │  │ AWS RDS MySQL 8.0 (db.t3.micro)    │  │  │  • Private & Encrypted     │  │
│  │  │  • Non-Publicly Accessible         │  │  └────────────────────────────┘  │
│  │  │  • Ingress strictly from EC2 SG    │  │  ┌────────────────────────────┐  │
│  │  └────────────────────────────────────┘  │  │ AWS CloudWatch             │  │
│  └──────────────────────────────────────────┘  │  • Health & Error Logging  │  │
│                                                └────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Technologies Used

- **Backend Framework**: Python 3 / Flask
- **Web Server**: Gunicorn WSGI managed by Linux `systemd`
- **Database Engine**: AWS RDS MySQL 8.0 / PyMySQL
- **Cloud Infrastructure**: AWS (EC2, RDS, VPC, S3, IAM, CloudWatch)
- **Infrastructure as Code (IaC)**: Terraform v1.5+
- **Frontend Architecture**: Responsive HTML5, Modern Vanilla CSS3 Design System, Vanilla JavaScript

---

## 📂 Project Folder Structure

```
d:/internship project/
├── app/
│   ├── static/
│   │   ├── css/
│   │   │   └── style.css            # Custom responsive design system
│   │   ├── js/
│   │   │   └── main.js              # POS calculator, modals, alerts
│   │   └── uploads/
│   │       └── .gitkeep             # Local image upload fallback directory
│   ├── templates/
│   │   ├── base.html                # Sidebar navigation & layout shell
│   │   ├── dashboard.html           # Executive KPI metrics & low stock table
│   │   ├── products.html            # Product catalog & modal forms
│   │   ├── stock.html               # Stock in/out adjustments & audit logs
│   │   ├── sales.html               # Point of Sale & real-time billing
│   │   └── reports.html             # Valuation reports & CSV export
│   ├── app.py                       # Main Flask web application routes
│   ├── config.py                    # Environment variable configuration loader
│   ├── database.py                  # PyMySQL connection pool & schema init
│   ├── s3_helper.py                 # AWS S3 image upload integration
│   └── requirements.txt             # Python runtime dependencies
├── terraform/
│   ├── provider.tf                  # AWS provider & default tagging
│   ├── variables.tf                 # Parameterized input variables
│   ├── vpc.tf                       # Custom VPC, Subnets, IGW, Route tables
│   ├── security_groups.tf           # Least-privilege firewall definitions
│   ├── iam.tf                       # EC2 IAM role, policies & instance profile
│   ├── s3.tf                        # Private encrypted S3 bucket
│   ├── rds.tf                       # Private MySQL DB instance & Subnet group
│   ├── ec2.tf                       # EC2 instance & AMI data source
│   ├── user_data.sh                 # Cloud-init bootstrap installation script
│   ├── outputs.tf                   # Public IP, DNS, RDS endpoint, S3 outputs
│   ├── terraform.tfvars.example     # Sample configuration file
│   └── main.tf                      # Root orchestration
├── .env.example                     # Environment template for local dev
├── .gitignore                       # Git exclusion rules
└── README.md                        # Documentation & deployment guide
```

---

## 📋 Prerequisites

Before deploying the project to AWS, ensure you have:

1. **AWS Account**: An active AWS account with administrative or developer permissions.
2. **AWS CLI v2**: Installed and configured (`aws configure`).
3. **Terraform**: Version 1.5.0 or later installed on your machine.
4. **Python 3.10+** (Optional, only needed if running locally): Installed with `pip`.

---

## ⚙️ AWS Configuration

Configure your AWS credentials locally:

```bash
aws configure
```

You will be prompted to enter:
- **AWS Access Key ID**: `YOUR_ACCESS_KEY`
- **AWS Secret Access Key**: `YOUR_SECRET_KEY`
- **Default region name**: `us-east-1` (or your preferred region)
- **Default output format**: `json`

---

## 🛠️ Step-by-Step Terraform Deployment Guide

### 1. Navigate to the Terraform Directory
```bash
cd terraform
```

### 2. Configure Deployment Variables
Create a `terraform.tfvars` file by copying the example template:
```bash
cp terraform.tfvars.example terraform.tfvars
```

Edit `terraform.tfvars` and set a secure database password:
```hcl
aws_region        = "us-east-1"
project_name      = "inventory-mgmt"
environment       = "dev"
ec2_instance_type = "t3.micro"
rds_instance_class = "db.t3.micro"
db_name           = "inventory_db"
db_username       = "admin"
db_password       = "YourStrongPassword2026!" # Replace with your password
```

### 3. Initialize Terraform
Downloads the required AWS provider plugins:
```bash
terraform init
```

### 4. Validate the Configuration
Checks the syntax and internal consistency of all Terraform files:
```bash
terraform validate
```

### 5. Preview Infrastructure Changes
Generates an execution plan showing all resources that will be provisioned:
```bash
terraform plan
```

### 6. Apply & Deploy Infrastructure
Executes the plan and builds the AWS cloud environment:
```bash
terraform apply
```
*Type `yes` when prompted to confirm.*

The deployment takes approximately 3 to 5 minutes (primarily for RDS MySQL database creation).

---

## 🌐 Accessing the Application

Once `terraform apply` finishes, inspect the output values:

```bash
# Example Output:
app_url          = "http://54.210.45.112"
ec2_public_ip    = "54.210.45.112"
rds_endpoint     = "inventory-mgmt-mysql-db.c7x8y9z0.us-east-1.rds.amazonaws.com"
s3_bucket_name   = "inventory-mgmt-assets-dev-a1b2c3d4"
```

1. Open your web browser and navigate to:
   ```
   http://<ec2_public_ip>
   ```
2. Verify live cloud health status:
   ```
   http://<ec2_public_ip>/health
   ```

---

## 🔗 How the Application Connects to AWS RDS

The application connects to AWS RDS using the following secure mechanism:

1. **Automated Endpoint Injection**: When Terraform provisions the RDS instance, its private endpoint address (`aws_db_instance.mysql.address`) is dynamically passed into the EC2 instance's `user_data.sh` script.
2. **Environment File**: The script securely writes `/etc/inventory_app.env` with `DB_HOST=<rds_endpoint>`, `DB_USER=<db_username>`, `DB_PASSWORD=<db_password>`, `DB_NAME=<db_name>`, and `DB_PORT=3306` with permissions restricted to `root` (`chmod 600`).
3. **Application Configuration**: `config.py` loads these variables at runtime using `python-dotenv`.
4. **PyMySQL Connection**: `database.py` creates a connection to the RDS MySQL instance on port 3306 and automatically creates the schema tables (`products`, `categories`, `sales`, `stock_logs`) and default seed data on the first request.
5. **Private Isolation**: The RDS instance is placed in private subnets with `publicly_accessible = false`. The RDS security group permits incoming MySQL connections **only** from the EC2 web server security group.

---

## 💻 Local Development Setup (Optional)

If you wish to test the application locally on your computer:

```bash
# 1. Navigate to the app folder
cd app

# 2. Create and activate a Python virtual environment
python -m venv venv
# On Windows:
.\venv\Scripts\activate
# On Linux/macOS:
source venv/bin/activate

# 3. Install requirements
pip install -r requirements.txt

# 4. Copy sample environment file
cp ../.env.example .env

# 5. Start Flask development server
python app.py
```
Open `http://localhost:5000` in your browser.

---

## 🧹 Destroying the Infrastructure

To prevent unnecessary AWS cloud charges after completing your testing or internship evaluation:

```bash
cd terraform
terraform destroy
```
*Type `yes` when prompted to tear down all provisioned resources (VPC, EC2, RDS, S3, IAM, Security Groups).*

---

## 🔒 Important Security Notes

- **Zero Hardcoded Secrets**: Sensitive credentials (passwords, access keys) are never stored in git or application source code.
- **Private Database Subnets**: The RDS database is completely shielded from public internet traffic.
- **IAM Instance Profile**: EC2 accesses AWS S3 using an IAM role—no static AWS access keys are stored on the server.
- **Cost Protection**: Free-tier eligible compute classes (`t3.micro` EC2, `db.t3.micro` RDS with 20 GB gp3 storage) and single-AZ configurations are utilized to ensure cost effectiveness for college and internship projects.
