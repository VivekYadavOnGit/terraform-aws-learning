# Terraform AWS Infrastructure Project --- Interview Preparation

## 1. Project at a Glance

**Project:** Terraform AWS Infrastructure\
**Role focus:** DevOps / Cloud / Infrastructure as Code\
**Level:** Beginner-friendly, fresher interview project

### One-line explanation

> I used Terraform as Infrastructure as Code to provision a basic AWS
> environment containing a VPC, public subnet, Internet Gateway, route
> table, security group, SSH key pair and an EC2 instance, then
> connected to the server through SSH and configured Nginx as a web
> server.

### Technologies Used

-   Terraform
-   AWS
-   EC2
-   VPC
-   Subnet
-   Internet Gateway
-   Route Table
-   Security Group
-   SSH
-   Linux
-   Nginx
-   Git / GitHub

------------------------------------------------------------------------

# 2. Project Architecture

``` text
                         AWS
                          |
                    +-----+------+
                    |    VPC     |
                    | 10.0.0.0/16|
                    +-----+------+
                          |
                    Public Subnet
                     10.0.1.0/24
                          |
                  +-------+--------+
                  | Route Table    |
                  | 0.0.0.0/0      |
                  +-------+--------+
                          |
                  Internet Gateway
                          |
                  +-------+--------+
                  | Security Group |
                  | SSH  : 22      |
                  | HTTP : 80      |
                  +-------+--------+
                          |
                       EC2
                     t3.micro
                          |
                       Nginx
                       Port 80
```

### Traffic flow

For HTTP:

``` text
Internet
   |
   v
EC2 Public IP
   |
   v
Security Group - Port 80
   |
   v
EC2 Instance
   |
   v
Nginx
```

For SSH:

``` text
Your WSL/Linux machine
        |
        | SSH using private key
        v
EC2 Public IP : 22
        |
        v
Security Group
        |
        v
EC2 Instance
```

------------------------------------------------------------------------

# 3. Why Did I Build This Project?

The main purpose was to learn Infrastructure as Code and AWS networking
practically.

Before Terraform, infrastructure could be created manually from the AWS
Console. With Terraform, I can define infrastructure in code and
reproduce it.

### Interview answer

> I built this project to get hands-on experience with Infrastructure as
> Code. Instead of manually creating AWS resources from the console, I
> used Terraform to define and provision the infrastructure. I also
> wanted to understand basic AWS networking, EC2 provisioning, security
> groups, SSH access and Linux server configuration.

------------------------------------------------------------------------

# 4. What Is Infrastructure as Code?

Infrastructure as Code, or IaC, is the practice of defining and managing
infrastructure using configuration files instead of manually creating
resources.

Examples of infrastructure include:

-   Servers
-   Networks
-   Databases
-   Security rules
-   Cloud resources

Terraform is an IaC tool.

### Benefits

-   Repeatable infrastructure
-   Version control
-   Automation
-   Easier changes
-   Consistency
-   Infrastructure can be reviewed like code

### Interview answer

> Infrastructure as Code means managing infrastructure through code or
> configuration files instead of manually creating it. Terraform is an
> IaC tool that allows me to define cloud resources and provision them
> automatically.

------------------------------------------------------------------------

# 5. What Is Terraform?

Terraform is an Infrastructure as Code tool developed by HashiCorp.

It uses declarative configuration files, normally written in HCL, to
define the desired infrastructure.

Example:

``` hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

This tells Terraform that I want an AWS VPC with the specified CIDR
block.

### Declarative vs Imperative

Terraform is mainly declarative.

**Declarative:**

> Describe what the final infrastructure should look like.

**Imperative:**

> Give the system step-by-step instructions for how to create it.

Terraform determines the actions required to reach the desired state.

------------------------------------------------------------------------

# 6. Terraform Files in This Project

``` text
terraform-aws-infrastructure/
│
├── main.tf
├── provider.tf
├── variables.tf
├── outputs.tf
├── README.md
├── .gitignore
└── .terraform.lock.hcl
```

Terraform also creates local state files, but they are intentionally
excluded from Git.

------------------------------------------------------------------------

# 7. provider.tf

The project uses the AWS provider.

``` hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }

  required_version = ">= 1.16.0"
}

provider "aws" {
  region = "us-east-1"
}
```

## What is a Terraform provider?

A provider is a plugin that allows Terraform to communicate with an
external platform or service.

Examples:

-   AWS provider
-   Azure provider
-   Google Cloud provider
-   Kubernetes provider

In this project, the AWS provider allows Terraform to communicate with
AWS.

### Interview answer

> A Terraform provider is responsible for communicating with an external
> API or platform. I used the AWS provider so Terraform could create and
> manage AWS resources.

------------------------------------------------------------------------

# 8. Terraform Version Constraint

``` hcl
required_version = ">= 1.16.0"
```

This specifies the Terraform version requirement for the project.

The project was developed using Terraform 1.16.x.

------------------------------------------------------------------------

# 9. AWS Region

``` hcl
provider "aws" {
  region = "us-east-1"
}
```

This tells Terraform to create AWS resources in the `us-east-1` region
unless a resource overrides the region.

### What is an AWS Region?

A region is a geographical area containing multiple AWS Availability
Zones.

Examples:

-   `us-east-1`
-   `ap-south-1`
-   `eu-west-1`

------------------------------------------------------------------------

# 10. VPC

Resource:

``` hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "terraform-vpc"
  }
}
```

## Definition

A VPC, or Virtual Private Cloud, is a logically isolated network in AWS
where you can launch resources such as EC2 instances.

Think of a VPC as your own private network inside AWS.

### CIDR

``` text
10.0.0.0/16
```

defines the IP address range of the VPC.

A `/16` network provides a large private address range.

### Why did I create a VPC?

> I created a VPC to provide an isolated network environment for the EC2
> infrastructure.

------------------------------------------------------------------------

# 11. Subnet

Resource:

``` hcl
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "terraform-public-subnet"
  }
}
```

## Definition

A subnet is a smaller network inside a VPC.

Our VPC:

``` text
10.0.0.0/16
```

contains the subnet:

``` text
10.0.1.0/24
```

### Why /24?

For this small learning project, `/24` provides enough addresses for the
subnet.

### Availability Zone

``` text
us-east-1a
```

The subnet is associated with this Availability Zone.

### What makes this subnet public?

The subnet has:

1.  A route to an Internet Gateway
2.  A public IP assigned to the EC2 instance

The route table provides the network path to the internet.

### Interview answer

> A subnet is a smaller network inside a VPC. I created a public subnet
> with CIDR 10.0.1.0/24 in us-east-1a. It is public because its route
> table sends internet-bound traffic to the Internet Gateway, and the
> EC2 instance receives a public IP.

------------------------------------------------------------------------

# 12. Internet Gateway

Resource:

``` hcl
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "terraform-igw"
  }
}
```

## Definition

An Internet Gateway, or IGW, provides a path between a VPC and the
public internet.

It is attached to the VPC.

### Simple analogy

Think of the VPC as a building.

The Internet Gateway is one of the doors connecting the building to the
internet.

### Interview answer

> An Internet Gateway allows resources in a VPC to communicate with the
> internet when the appropriate routing and public addressing are
> configured.

------------------------------------------------------------------------

# 13. Route Table

Resource:

``` hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }

  tags = {
    Name = "terraform-public-route-table"
  }
}
```

## Definition

A route table contains rules that determine where network traffic should
go.

The important route is:

``` text
0.0.0.0/0 → Internet Gateway
```

### What does 0.0.0.0/0 mean?

It represents all IPv4 destinations that are not otherwise matched by a
more specific route.

So:

``` text
0.0.0.0/0 → IGW
```

means internet-bound IPv4 traffic should go through the Internet
Gateway.

### Interview answer

> The route table controls traffic routing. I added a default route,
> 0.0.0.0/0, pointing to the Internet Gateway so the public subnet can
> reach the internet.

------------------------------------------------------------------------

# 14. Route Table Association

Resource:

``` hcl
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}
```

## Definition

A route table association connects a subnet to a route table.

The route table itself exists independently, so AWS needs to know which
subnet should use it.

In this project:

``` text
Public Subnet
      |
      v
Public Route Table
      |
      v
Internet Gateway
```

### Interview answer

> The route table association attaches the public subnet to the route
> table containing the route to the Internet Gateway.

------------------------------------------------------------------------

# 15. Security Group

Resource:

``` hcl
resource "aws_security_group" "web" {
  name   = "terraform-web-sg"
  vpc_id = aws_vpc.main.id

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

## Definition

A Security Group is a virtual firewall for AWS resources such as EC2
instances.

It controls inbound and outbound traffic.

### Port 22

``` text
22/TCP
```

is used for SSH.

It allows me to remotely connect to the Linux EC2 server.

### Port 80

``` text
80/TCP
```

is used for HTTP.

It allows users to access the Nginx web server.

### Egress

The project allows outbound traffic:

``` text
0.0.0.0/0
```

### Important security note

For learning, this project allows SSH and HTTP from:

``` text
0.0.0.0/0
```

This means any IPv4 address can attempt to connect.

For production, SSH should preferably be restricted to a trusted IP
range or accessed through a more secure architecture.

### Interview answer

> A Security Group acts as a virtual firewall for the EC2 instance. In
> this project I allowed TCP port 22 for SSH and TCP port 80 for HTTP.
> For a production environment, I would restrict SSH access rather than
> allowing it from 0.0.0.0/0.

------------------------------------------------------------------------

# 16. SSH Key Pair

Resource:

``` hcl
resource "aws_key_pair" "main" {
  key_name   = "terraform-key"
  public_key = file("~/.ssh/terraform-key.pub")
}
```

## Definition

An EC2 key pair is used for secure SSH authentication.

It contains:

``` text
Public key  → AWS / EC2
Private key → My machine
```

The private key should never be shared.

### SSH command used

``` bash
ssh -i ~/.ssh/terraform-key ec2-user@<EC2_PUBLIC_IP>
```

### Interview answer

> I used an EC2 key pair for SSH authentication. Terraform uploaded the
> public key to AWS, while the private key remained on my local machine.
> I then used the private key to securely connect to the EC2 instance.

------------------------------------------------------------------------

# 17. EC2 Instance

Resource:

``` hcl
resource "aws_instance" "web" {
  ami                         = "ami-07f9c6534b9c70941"
  instance_type               = "t3.micro"
  subnet_id                   = aws_subnet.public.id
  vpc_security_group_ids     = [aws_security_group.web.id]
  key_name                    = aws_key_pair.main.key_name
  associate_public_ip_address = true

  tags = {
    Name = "terraform-web-server"
  }
}
```

## Definition

Amazon EC2, or Elastic Compute Cloud, provides virtual servers in AWS.

In this project I used:

``` text
Instance type: t3.micro
```

### AMI

AMI means Amazon Machine Image.

It provides the operating system and initial software configuration for
the EC2 instance.

The project uses an Amazon Linux 2023 AMI.

### Instance Type

The instance type determines the compute resources available to the
instance.

`t3.micro` is a small instance suitable for this learning project.

### Public IP

``` hcl
associate_public_ip_address = true
```

allows the instance to receive a public IPv4 address.

That public IP was used for SSH and HTTP access.

### Interview answer

> EC2 is AWS's virtual server service. I provisioned a t3.micro instance
> using an Amazon Linux AMI, placed it in my public subnet, attached the
> security group and SSH key pair, and assigned a public IP so I could
> access it remotely.

------------------------------------------------------------------------

# 18. Nginx

Nginx was installed after connecting to the EC2 instance.

``` bash
sudo dnf install nginx -y
```

Started with:

``` bash
sudo systemctl start nginx
```

Checked with:

``` bash
sudo systemctl status nginx
```

The server listens on HTTP port 80.

I verified it using:

``` bash
curl http://<EC2_PUBLIC_IP>
```

and through a browser.

### What is Nginx?

Nginx is a web server and reverse proxy commonly used to serve HTTP
content.

For this project, it was simply used as a web server to verify that the
EC2 networking configuration worked.

------------------------------------------------------------------------

# 19. Terraform State

Terraform maintains a state file:

``` text
terraform.tfstate
```

## Definition

Terraform state records information about resources that Terraform
manages.

It helps Terraform understand:

-   What resources exist
-   Resource IDs
-   Current infrastructure information
-   Relationships between resources

### Why is state important?

Suppose Terraform created an EC2 instance.

Terraform needs to know which AWS EC2 instance corresponds to:

``` hcl
aws_instance.web
```

The state helps maintain this mapping.

### Important

The state file should generally not be committed to a public Git
repository because it may contain sensitive infrastructure information.

This project excludes it using `.gitignore`.

------------------------------------------------------------------------

# 20. .gitignore

The project ignores:

``` text
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.pem
```

### Why?

`.terraform/`

Contains downloaded provider/plugin files.

`*.tfstate`

Contains Terraform state.

`*.tfvars`

May contain variable values and potentially sensitive information.

`*.pem`

Private key files should not be committed.

### Important

The project DOES commit:

``` text
.terraform.lock.hcl
```

The lock file helps keep provider versions consistent.

------------------------------------------------------------------------

# 21. Terraform Dependency

Example:

``` hcl
vpc_id = aws_vpc.main.id
```

Terraform understands that the subnet depends on the VPC.

Another example:

``` hcl
gateway_id = aws_internet_gateway.main.id
```

Terraform understands that the route depends on the Internet Gateway.

This is called an implicit dependency.

### Interview answer

> Terraform can understand dependencies through resource references. For
> example, when I use aws_vpc.main.id inside a subnet, Terraform knows
> the VPC must exist before creating the subnet.

------------------------------------------------------------------------

# 22. Terraform Workflow

The workflow used in this project is:

``` text
Write Terraform configuration
          |
          v
terraform fmt
          |
          v
terraform validate
          |
          v
terraform plan
          |
          v
terraform apply
          |
          v
Verify infrastructure
```

------------------------------------------------------------------------

# 23. terraform init

Command:

``` bash
terraform init
```

## What does it do?

It initializes the Terraform working directory.

It downloads required providers and prepares the project.

Example:

``` text
Terraform configuration
        |
        v
terraform init
        |
        v
AWS provider downloaded
```

### Interview answer

> terraform init initializes the Terraform project and downloads the
> required provider plugins.

------------------------------------------------------------------------

# 24. terraform fmt

Command:

``` bash
terraform fmt
```

## What does it do?

It formats Terraform configuration files according to Terraform's
standard formatting.

It improves readability and consistency.

------------------------------------------------------------------------

# 25. terraform validate

Command:

``` bash
terraform validate
```

## What does it do?

It checks whether the Terraform configuration is syntactically valid and
internally consistent.

It does not create infrastructure.

### Difference from plan

``` text
validate → configuration correctness
plan     → expected infrastructure changes
```

------------------------------------------------------------------------

# 26. terraform plan

Command:

``` bash
terraform plan
```

## Definition

`terraform plan` previews the changes Terraform would make.

It does not normally apply those changes.

Example:

``` text
+ create
~ change
- destroy
```

### Interview answer

> I use terraform plan before apply to review what Terraform intends to
> change. It helps catch unintended changes before modifying
> infrastructure.

------------------------------------------------------------------------

# 27. terraform apply

Command:

``` bash
terraform apply
```

## Definition

`terraform apply` applies the Terraform configuration and creates or
modifies infrastructure.

In this project it created:

``` text
8 resources
```

including the VPC, subnet, gateway, route table, security group, key
pair and EC2 instance.

------------------------------------------------------------------------

# 28. terraform state list

Command:

``` bash
terraform state list
```

It displays resources currently managed in the Terraform state.

The project state contained:

``` text
aws_instance.web
aws_internet_gateway.main
aws_key_pair.main
aws_route_table.public
aws_route_table_association.public
aws_security_group.web
aws_subnet.public
aws_vpc.main
```

------------------------------------------------------------------------

# 29. Terraform Output

The project uses:

``` hcl
output "ec2_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.web.public_ip
}
```

This allows:

``` bash
terraform output
```

to display the EC2 public IP.

### Why use outputs?

Outputs expose useful values after Terraform creates resources.

------------------------------------------------------------------------

# 30. terraform destroy

Command:

``` bash
terraform destroy
```

## Definition

It removes resources managed by the Terraform configuration.

### Why is this important?

Cloud resources can cost money.

After finishing the learning project, destroying unused resources helps
avoid unnecessary AWS charges.

### Interview answer

> terraform destroy removes infrastructure managed by the current
> Terraform configuration. I would use it when the learning environment
> is no longer needed to avoid unnecessary AWS costs.

------------------------------------------------------------------------

# 31. Complete Commands Used

## Initialize

``` bash
terraform init
```

## Format

``` bash
terraform fmt
```

## Validate

``` bash
terraform validate
```

## Preview

``` bash
terraform plan
```

## Provision

``` bash
terraform apply
```

## Check outputs

``` bash
terraform output
```

## Check state

``` bash
terraform state list
```

## Destroy

``` bash
terraform destroy
```

## Git

``` bash
git status
git add .
git commit -m "Initial Terraform AWS infrastructure"
git push
```

------------------------------------------------------------------------

# 32. Git and GitHub in This Project

I used Git to version-control the Terraform project.

The project was pushed to GitHub using SSH authentication.

### Why Git?

Terraform configuration is code, so Git allows me to:

-   Track changes
-   Create commits
-   Review history
-   Collaborate
-   Store the project remotely

### Why SSH for GitHub?

GitHub does not accept normal account passwords for Git operations over
HTTPS.

I configured an SSH key and connected the repository using:

``` text
git@github.com:VivekYadavOnGit/terraform-aws-learning.git
```

------------------------------------------------------------------------

# 33. Most Important Interview Question

## Q: Explain your Terraform project.

### Strong fresher answer

> I built a beginner-friendly AWS Infrastructure as Code project using
> Terraform. I created a VPC with a 10.0.0.0/16 CIDR, a public subnet
> with a 10.0.1.0/24 CIDR, an Internet Gateway and a route table with a
> default route to the Internet Gateway. I also created a Security Group
> allowing SSH on port 22 and HTTP on port 80. Then I provisioned a
> t3.micro EC2 instance using an Amazon Linux AMI and an SSH key pair.
> After connecting to the instance through SSH, I installed Nginx and
> verified that it was accessible through the EC2 public IP. I used
> Terraform state to track the infrastructure and Git/GitHub for version
> control.

------------------------------------------------------------------------

# 34. Q: Why Terraform instead of creating resources manually?

### Answer

> Manual provisioning works for small environments, but it becomes
> difficult to repeat and maintain. Terraform lets me define
> infrastructure as code, version it with Git and reproduce the same
> environment consistently.

------------------------------------------------------------------------

# 35. Q: Why did you use Terraform?

### Answer

> I wanted to learn Infrastructure as Code and automate AWS
> infrastructure provisioning. Terraform also helped me understand AWS
> networking while practicing version-controlled infrastructure.

------------------------------------------------------------------------

# 36. Q: What resources did you create?

### Answer

> I created a VPC, public subnet, Internet Gateway, route table, route
> table association, Security Group, EC2 key pair and EC2 instance.

------------------------------------------------------------------------

# 37. Q: Why do you need a VPC?

### Answer

> A VPC provides an isolated virtual network in AWS where I can place
> and control resources such as EC2 instances.

------------------------------------------------------------------------

# 38. Q: What is the difference between VPC and subnet?

### Answer

> A VPC is the overall virtual network, while a subnet is a smaller
> network segment inside the VPC. In my project, the VPC uses
> 10.0.0.0/16 and the public subnet uses 10.0.1.0/24.

------------------------------------------------------------------------

# 39. Q: What makes your subnet public?

### Answer

> The subnet has a route to an Internet Gateway through its route table,
> and the EC2 instance receives a public IP. Together these allow
> internet connectivity.

------------------------------------------------------------------------

# 40. Q: What does 0.0.0.0/0 mean?

### Answer

> It represents all IPv4 destinations. In my route table, 0.0.0.0/0
> points to the Internet Gateway, so internet-bound traffic can leave
> the VPC.

------------------------------------------------------------------------

# 41. Q: What is an Internet Gateway?

### Answer

> An Internet Gateway is an AWS component that provides connectivity
> between a VPC and the public internet when routing and public
> addressing are configured correctly.

------------------------------------------------------------------------

# 42. Q: What is a route table?

### Answer

> A route table contains routing rules that determine where network
> traffic should go. My public route table sends 0.0.0.0/0 traffic to
> the Internet Gateway.

------------------------------------------------------------------------

# 43. Q: What is a Security Group?

### Answer

> A Security Group is a virtual firewall attached to resources such as
> EC2. It controls allowed inbound and outbound traffic.

------------------------------------------------------------------------

# 44. Q: Why did you allow port 22?

### Answer

> Port 22 is the default SSH port. I needed it to connect remotely to
> the Linux EC2 instance.

------------------------------------------------------------------------

# 45. Q: Why did you allow port 80?

### Answer

> Port 80 is the default HTTP port. Nginx was configured as a web
> server, so port 80 was required to access it through the browser or
> curl.

------------------------------------------------------------------------

# 46. Q: Is allowing SSH from 0.0.0.0/0 secure?

### Answer

> It is acceptable for a temporary learning project, but it is not a
> good production practice. In production I would restrict SSH to
> trusted IP addresses or use a more secure access method.

------------------------------------------------------------------------

# 47. Q: What is an AMI?

### Answer

> An AMI, or Amazon Machine Image, is a template used to launch EC2
> instances. It contains the operating system and initial configuration
> needed to start the instance.

------------------------------------------------------------------------

# 48. Q: Why did you use t3.micro?

### Answer

> I used t3.micro because this was a small learning project and it
> provided enough resources for running a basic Linux server and Nginx.

------------------------------------------------------------------------

# 49. Q: How did you connect to EC2?

### Answer

``` bash
ssh -i ~/.ssh/terraform-key ec2-user@<EC2_PUBLIC_IP>
```

### Explanation

> I used SSH with the private key corresponding to the public key
> configured in the EC2 key pair.

------------------------------------------------------------------------

# 50. Q: Why should the private key not be uploaded to GitHub?

### Answer

> The private key is a credential used for authentication. If exposed,
> someone could potentially access the server. Therefore private keys
> should never be committed to Git.

------------------------------------------------------------------------

# 51. Q: What is Terraform state?

### Answer

> Terraform state stores information about infrastructure managed by
> Terraform. It allows Terraform to map configuration resources to real
> resources and determine what needs to be created, changed or
> destroyed.

------------------------------------------------------------------------

# 52. Q: Should terraform.tfstate be committed to Git?

### Answer

> Normally I should not commit it to a public Git repository because
> state can contain sensitive infrastructure information. In my project
> I excluded it through .gitignore.

------------------------------------------------------------------------

# 53. Q: What is terraform.tfstate.backup?

### Answer

> Terraform can maintain a backup of the previous state file. It can
> help recover the previous state if needed. Like the main state file,
> it should not be committed to Git.

------------------------------------------------------------------------

# 54. Q: What is .terraform.lock.hcl?

### Answer

> It is the Terraform dependency lock file. It records provider
> dependency information and helps ensure consistent provider versions
> across runs. Unlike the state file, it should normally be committed to
> Git.

------------------------------------------------------------------------

# 55. Q: What happens when you run terraform init?

### Answer

> Terraform initializes the working directory and downloads the required
> provider plugins based on the configuration.

------------------------------------------------------------------------

# 56. Q: What is the difference between init, plan and apply?

### Answer

``` text
terraform init
→ prepares the project and downloads providers

terraform plan
→ previews infrastructure changes

terraform apply
→ actually applies those changes
```

------------------------------------------------------------------------

# 57. Q: What is terraform validate?

### Answer

> It checks whether the Terraform configuration is syntactically valid
> and internally consistent. It does not provision infrastructure.

------------------------------------------------------------------------

# 58. Q: What is terraform fmt?

### Answer

> It automatically formats Terraform configuration files according to
> Terraform's standard formatting.

------------------------------------------------------------------------

# 59. Q: How does Terraform know what to create first?

### Answer

> Terraform builds a dependency graph from resource references. For
> example, a subnet references the VPC ID, so Terraform knows the VPC
> must exist before the subnet.

------------------------------------------------------------------------

# 60. Q: What is a Terraform resource?

### Answer

> A resource represents an infrastructure object that Terraform manages.
> Examples in my project include aws_vpc, aws_subnet, aws_instance and
> aws_security_group.

------------------------------------------------------------------------

# 61. Q: What is a Terraform provider?

### Answer

> A provider is a plugin that allows Terraform to interact with an
> external platform. I used the AWS provider to manage AWS
> infrastructure.

------------------------------------------------------------------------

# 62. Q: What happens if you change a Terraform configuration?

### Answer

> I would first run terraform fmt and terraform validate, then terraform
> plan to see the proposed changes. If the plan is correct, I would run
> terraform apply.

------------------------------------------------------------------------

# 63. Q: What happens if you manually delete an EC2 instance from AWS?

### Answer

> Terraform state may still contain the resource. During a later plan or
> refresh-related operation, Terraform can detect that the real resource
> is missing and may propose creating it again to match the
> configuration.

------------------------------------------------------------------------

# 64. Q: What happens if you run terraform apply twice?

### Answer

> If there are no configuration or infrastructure changes, Terraform
> should normally report that there are no changes to apply. Terraform
> is designed to maintain the desired state rather than blindly creating
> duplicate resources.

------------------------------------------------------------------------

# 65. Q: How would you make this project more secure?

### Answer

> I would restrict SSH access to a trusted IP instead of 0.0.0.0/0,
> avoid exposing unnecessary ports, use least-privilege IAM permissions,
> protect Terraform state using a secure remote backend, avoid
> hardcoding sensitive values, and use stronger server access controls.

------------------------------------------------------------------------

# 66. Q: How would you make this project production-ready?

### Answer

A production implementation could include:

-   Remote Terraform state
-   State locking
-   Restricted Security Group rules
-   IAM least privilege
-   Multiple Availability Zones
-   Private subnets
-   NAT Gateway where required
-   Load Balancer
-   Auto Scaling
-   HTTPS/TLS
-   Monitoring and logging
-   CI/CD for Terraform
-   Separate environments

### Important

Do not claim these were implemented in this project.

Say:

> These are improvements I would consider for a production environment.

------------------------------------------------------------------------

# 67. Troubleshooting Questions

## Q: Terraform says provider is missing.

### Possible solution

Run:

``` bash
terraform init
```

The provider may not have been initialized.

------------------------------------------------------------------------

## Q: terraform validate fails.

### Approach

Run:

``` bash
terraform validate
```

Read the error carefully.

Then check:

-   HCL syntax
-   Resource names
-   Missing braces
-   Incorrect arguments
-   Resource references

------------------------------------------------------------------------

## Q: EC2 is running but I cannot SSH.

Check:

``` text
1. EC2 is running
2. Correct public IP
3. Port 22 allowed
4. Correct private key
5. Private key permissions
6. Correct SSH username
```

Command:

``` bash
chmod 400 ~/.ssh/terraform-key
```

Then:

``` bash
ssh -i ~/.ssh/terraform-key ec2-user@<EC2_PUBLIC_IP>
```

------------------------------------------------------------------------

# 68. Troubleshooting: Nginx Not Accessible

Check:

### 1. Nginx status

``` bash
sudo systemctl status nginx
```

### 2. Is Nginx running?

``` bash
sudo systemctl start nginx
```

### 3. Is port 80 allowed in Security Group?

The project allows:

``` text
TCP 80
```

### 4. Test locally on EC2

``` bash
curl http://localhost
```

### 5. Test from your machine

``` bash
curl http://<EC2_PUBLIC_IP>
```

------------------------------------------------------------------------

# 69. Troubleshooting: EC2 Has No Internet

Check the architecture:

``` text
EC2
 |
Public Subnet
 |
Route Table
 |
0.0.0.0/0
 |
Internet Gateway
 |
Internet
```

Verify:

-   EC2 has a public IP
-   Route table is associated with subnet
-   Default route points to Internet Gateway
-   Security Group allows outbound traffic
-   Internet Gateway is attached to VPC

------------------------------------------------------------------------

# 70. Important AWS Networking Concepts

Memorize these relationships:

``` text
VPC
 |
 +-- Subnet
       |
       +-- Route Table Association
       |
       +-- EC2
             |
             +-- Security Group
```

And:

``` text
Public Subnet
      |
Route Table
      |
0.0.0.0/0
      |
Internet Gateway
      |
Internet
```

------------------------------------------------------------------------

# 71. Resource Quick Revision Table

  Resource            Simple Definition                Used For
  ------------------- -------------------------------- --------------------------
  VPC                 Isolated AWS network             Main network
  Subnet              Smaller network inside VPC       Place EC2
  Internet Gateway    VPC-to-internet gateway          Internet connectivity
  Route Table         Traffic routing rules            Send traffic to IGW
  Route Association   Connects subnet to route table   Apply routing
  Security Group      Virtual firewall                 Allow SSH/HTTP
  Key Pair            SSH authentication               Secure EC2 login
  EC2                 Virtual server                   Run Linux/Nginx
  Nginx               Web server                       Serve HTTP traffic
  AMI                 EC2 server template              Launch OS
  Terraform State     Tracks managed resources         State management
  Provider            Terraform integration plugin     Connect Terraform to AWS

------------------------------------------------------------------------

# 72. Important Ports

  Port   Protocol   Purpose
  ------ ---------- ---------
  22     TCP        SSH
  80     TCP        HTTP
  443    TCP        HTTPS

Only ports 22 and 80 were required in this project.

------------------------------------------------------------------------

# 73. Important CIDR Values

### VPC

``` text
10.0.0.0/16
```

Large private network.

### Public subnet

``` text
10.0.1.0/24
```

Smaller network inside the VPC.

### Internet route

``` text
0.0.0.0/0
```

All IPv4 destinations.

------------------------------------------------------------------------

# 74. Interview Scenario: Add HTTPS

### Question

> Suppose you want to make Nginx available over HTTPS. What would you
> change?

### Answer

> I would allow TCP port 443 in the Security Group and configure an
> SSL/TLS certificate and HTTPS listener or configure Nginx with the
> certificate. In a production setup I would generally prefer managed
> certificates and an appropriate load-balancing architecture.

------------------------------------------------------------------------

# 75. Interview Scenario: Restrict SSH

### Question

> How would you prevent everyone on the internet from accessing SSH?

### Answer

Instead of:

``` hcl
cidr_blocks = ["0.0.0.0/0"]
```

I would restrict it to a trusted public IP or network range.

For example:

``` hcl
cidr_blocks = ["YOUR_PUBLIC_IP/32"]
```

The exact IP should be replaced with the trusted administrator's public
IP.

------------------------------------------------------------------------

# 76. Interview Scenario: Change EC2 Name

Suppose:

``` hcl
Name = "terraform-web-server"
```

is changed to:

``` hcl
Name = "terraform-production-server"
```

Run:

``` bash
terraform fmt
terraform validate
terraform plan
```

Terraform will show the change it intends to make.

If the plan is correct:

``` bash
terraform apply
```

### Key concept

A change to configuration does not automatically change AWS.

Terraform must apply the change.

------------------------------------------------------------------------

# 77. Interview Scenario: Destroy Everything

Question:

> How would you remove the infrastructure created by Terraform?

Answer:

``` bash
terraform destroy
```

Then confirm the operation.

### Important

Do not run `terraform destroy` against a shared or production
environment without understanding the consequences.

------------------------------------------------------------------------

# 78. Interview Scenario: Why Git?

### Answer

> Terraform files are infrastructure code, so I used Git to track
> changes, create commits and maintain the project history. I pushed the
> project to GitHub so the project can be reviewed and shared.

------------------------------------------------------------------------

# 79. Interview Scenario: Why did you use SSH for GitHub?

### Answer

> GitHub does not support normal account passwords for Git operations
> over HTTPS. I configured an ED25519 SSH key and used the SSH
> repository URL to authenticate securely.

------------------------------------------------------------------------

# 80. Your Actual Project Resources

You should be able to remember these eight Terraform resources:

``` text
1. aws_vpc.main
2. aws_internet_gateway.main
3. aws_subnet.public
4. aws_route_table.public
5. aws_route_table_association.public
6. aws_security_group.web
7. aws_key_pair.main
8. aws_instance.web
```

------------------------------------------------------------------------

# 81. 30-Second Interview Explanation

> I built a Terraform-based AWS infrastructure project to practice
> Infrastructure as Code. I provisioned a VPC, public subnet, Internet
> Gateway, route table, Security Group, SSH key pair and a t3.micro EC2
> instance. I configured the network so the EC2 instance could access
> the internet and accept SSH and HTTP traffic. After connecting through
> SSH, I installed Nginx and verified the web server through the EC2
> public IP. I used Terraform state to manage the infrastructure and
> Git/GitHub for version control.

------------------------------------------------------------------------

# 82. 60-Second Interview Explanation

> My project focuses on AWS Infrastructure as Code using Terraform. I
> started by creating a VPC with a 10.0.0.0/16 CIDR and a public subnet
> using 10.0.1.0/24. I created an Internet Gateway and a route table
> with a 0.0.0.0/0 route to the Internet Gateway, then associated that
> route table with the subnet. I created a Security Group that allows
> SSH on port 22 and HTTP on port 80. Finally, I provisioned a t3.micro
> EC2 instance using an Amazon Linux AMI and an SSH key pair. I
> connected to the server using SSH, installed Nginx and verified HTTP
> access. I used Terraform commands such as init, validate, plan, apply
> and state list, and stored the project in GitHub using Git.

------------------------------------------------------------------------

# 83. Questions You Should Be Able to Answer Without Looking

Before the interview, make sure you can explain:

### Terraform

-   What is Terraform?
-   What is IaC?
-   What is a provider?
-   What is a resource?
-   What is Terraform state?
-   What does terraform init do?
-   What does terraform plan do?
-   What does terraform apply do?
-   What does terraform destroy do?
-   What does terraform validate do?
-   What does terraform fmt do?
-   Why use Git with Terraform?

### AWS

-   What is a VPC?
-   What is a subnet?
-   What is a public subnet?
-   What is an Internet Gateway?
-   What is a route table?
-   What is a Security Group?
-   What is EC2?
-   What is an AMI?
-   What is a key pair?
-   What is a public IP?

### Networking

-   What is CIDR?
-   What is 10.0.0.0/16?
-   What is 10.0.1.0/24?
-   What is 0.0.0.0/0?
-   What is port 22?
-   What is port 80?
-   How does HTTP reach Nginx?
-   How does SSH reach EC2?

### Linux

-   How do you SSH into EC2?
-   How do you install Nginx?
-   How do you start a service?
-   How do you check service status?
-   How do you test HTTP using curl?

------------------------------------------------------------------------

# 84. Commands You Should Practice Before Interview

Run these in your project directory:

``` bash
terraform fmt
terraform validate
terraform plan
terraform state list
terraform output
```

Then review:

``` bash
cat provider.tf
cat main.tf
cat outputs.tf
cat variables.tf
```

Check Git:

``` bash
git status
git log --oneline
git remote -v
```

------------------------------------------------------------------------

# 85. Important Things NOT to Claim

This project is intentionally beginner-friendly.

Do not claim that you implemented:

-   Kubernetes
-   Terraform modules
-   Remote state
-   S3 backend
-   State locking
-   Auto Scaling
-   Load Balancer
-   Multi-AZ architecture
-   CI/CD for Terraform
-   Production-grade security
-   Monitoring
-   Ansible automation

unless you actually implement them.

If asked about them, say:

> I have not implemented that yet, but I understand the purpose and it
> would be one of the next improvements I would make.

This is much better than pretending to have experience.

------------------------------------------------------------------------

# 86. Honest Fresher Positioning

If the interviewer asks:

> How much Terraform experience do you have?

Answer honestly:

> I am currently building my Terraform skills through hands-on AWS
> projects. This project helped me understand the fundamentals of
> Terraform, including providers, resources, state, dependencies, plan
> and apply, along with AWS networking and EC2 provisioning. I am still
> learning advanced Terraform concepts, so my current strength is the
> fundamentals and practical implementation.

------------------------------------------------------------------------

# 87. Final Revision Checklist

Before the interview, you should be able to draw this from memory:

``` text
                    Internet
                       |
                       v
              Internet Gateway
                       |
                       v
                  Route Table
                       |
                       v
                Public Subnet
                 10.0.1.0/24
                       |
                       v
                  Security Group
                  /           \
              SSH 22        HTTP 80
                  \           /
                       EC2
                     t3.micro
                       |
                     Nginx
```

And explain:

``` text
Terraform
   |
   +-- Provider
   |
   +-- VPC
   |     |
   |     +-- Subnet
   |           |
   |           +-- Route Table
   |           +-- EC2
   |
   +-- Internet Gateway
   |
   +-- Security Group
   |
   +-- Key Pair
```

------------------------------------------------------------------------

# 88. Final Interview Formula

When explaining any project, use:

``` text
1. What did I build?
2. Why did I build it?
3. What technologies did I use?
4. How does the architecture work?
5. What did I personally configure?
6. What problem did I face?
7. How did I troubleshoot it?
8. What did I learn?
9. What would I improve?
```

For this project, your strongest topics are:

``` text
Terraform
   ↓
AWS
   ↓
VPC Networking
   ↓
EC2
   ↓
Security Group
   ↓
SSH
   ↓
Linux
   ↓
Nginx
   ↓
Git/GitHub
```

------------------------------------------------------------------------

# 89. Final One-Line Project Description for Resume

> Provisioned AWS infrastructure using Terraform, including VPC, public
> subnet, Internet Gateway, route table, Security Group, SSH key pair
> and EC2, then configured and verified an Nginx web server through SSH.

------------------------------------------------------------------------

# 90. Final Advice

For a fresher interview, **do not try to memorize every Terraform
argument**.

Focus on understanding:

``` text
WHY → WHAT → HOW
```

For example:

**Why Security Group?**

> To control network traffic to the EC2 instance.

**What did I allow?**

> SSH on 22 and HTTP on 80.

**How did I configure it?**

> Using an AWS Security Group resource in Terraform.

If you can explain the architecture and the reason behind every
resource, you can confidently handle most beginner-level Terraform/AWS
questions about this project.
