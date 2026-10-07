# Terraform AWS Infrastructure

A beginner-friendly Infrastructure as Code (IaC) project using Terraform to provision and manage AWS infrastructure.

## Project Overview

This project uses Terraform to create a basic AWS environment containing:

- VPC
- Public Subnet
- Internet Gateway
- Route Table
- Security Group
- EC2 Instance
- SSH Key Pair

After provisioning the EC2 instance, Nginx was installed and configured as a web server.

## Architecture

```text
                    AWS
                     |
                    VPC
                10.0.0.0/16
                     |
              Public Subnet
                10.0.1.0/24
                     |
              Internet Gateway
                     |
                Route Table
                     |
              Security Group
                /         \
             SSH 22      HTTP 80
                \         /
                  EC2
                t3.micro
                   |
                 Nginx
```

## Technologies Used
- Terraform
- AWS
- EC2
- VPC
- Linux
- SSH
- Nginx
- Git/GitHub

## AWS Resources Created
Terraform provisions the following resources:
- aws_vpc
- aws_subnet
- aws_internet_gateway
- aws_route_table
- aws_route_table_association
- aws_security_group
- aws_key_pair
- aws_instance

## Terraform Commands
Initialize the project:
```bash
terraform init
```

## Format Terraform files:
```bash
terraform fmt
```

## Validate the configuration:
```bash
terraform validate
```

## Preview infrastructure changes:
```bash
terraform plan
```

## Create the infrastructure:
```bash
terraform apply
```

## View Terraform-managed resources:
```bash
terraform state list
```

## Display the EC2 public IP:
```bash
terraform output
```

## Destroy the infrastructure:
```bash
terraform destroy
```

## EC2 Access
The EC2 instance can be accessed using SSH:
```bash
ssh -i ~/.ssh/terraform-key ec2-user@<EC2_PUBLIC_IP>
```

## Nginx
Nginx was installed on the EC2 instance:
```bash
sudo dnf install nginx -y
```

## Start Nginx:
```bash
sudo systemctl start nginx
```

## Check Nginx status:
```bash
sudo systemctl status nginx
```

The web server can be accessed using:
```bash
http://<EC2_PUBLIC_IP>
```

## What I Learned
- Infrastructure as Code using Terraform
- AWS VPC networking basics
- Public subnet configuration
- Internet Gateway and route tables
- AWS Security Groups
- EC2 provisioning using Terraform
- SSH-based server access
- Basic Linux server management
- Nginx installation and configuration
- Terraform state management
- Git version control

## Project Goal
The goal of this project was to gain practical experience with Terraform and AWS infrastructure provisioning as part of my DevOps learning journey.

## Cleanup
To avoid unnecessary AWS charges, destroy the infrastructure after completing the project:
```bash
terraform destroy
```
