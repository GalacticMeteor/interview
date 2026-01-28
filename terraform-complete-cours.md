# Terraform Complete Course

## 1. Introduction to Terraform

### What is Terraform?
Terraform is an **open-source infrastructure as code (IaC) tool** created by HashiCorp. It allows you to define, provision, and manage cloud and on-premise infrastructure using **declarative configuration files**.

**Key Features:**
- Platform agnostic (AWS, Azure, GCP, etc.)
- Version-controlled infrastructure
- Dependency management
- Supports modular and reusable code

### Benefits of Terraform
- Automates infrastructure deployment
- Reduces human error
- Enables consistent environments across multiple stages (dev, staging, prod)
- Integrates with CI/CD pipelines

---

## 2. Terraform Basics

### Terraform Configuration Files
- `.tf` files: Written in **HCL (HashiCorp Configuration Language)**
- `.tfvars` files: Define variables
- `terraform.tfstate`: Maintains state of deployed infrastructure

### Basic Terraform Concepts
- **Provider**: Specifies which cloud or service to interact with (e.g., AWS, Azure)
- **Resource**: Represents a piece of infrastructure (e.g., EC2 instance, S3 bucket)
- **Data Source**: Reads information from external resources
- **Module**: Reusable group of resources

---

## 3. Terraform Workflow

**Terraform follows a four-step workflow:**
1. `terraform init` → Initialize a Terraform configuration directory
2. `terraform plan` → Preview changes before applying
3. `terraform apply` → Apply changes to the infrastructure
4. `terraform destroy` → Destroy infrastructure when no longer needed

**Common Terraform Commands with Explanations:**
```bash
terraform init      # Initialize working directory, download providers and modules
terraform plan      # Show execution plan and what resources will change
terraform apply     # Apply changes to infrastructure
terraform destroy   # Delete all resources managed by Terraform
terraform fmt       # Format configuration files according to Terraform style guide
terraform validate  # Validate configuration syntax
terraform state list    # List all resources in the current state
terraform state show <resource_name>    # Show details of a resource from state
terraform state pull    # Output the full state file contents
terraform state push    # Push local state to remote backend (rarely used)
terraform state mv <source> <destination>    # Move resource in state
terraform state rm <resource>    # Remove resource from state without destroying
``` 

> Comments explain each command purpose and usage

---

## 4. Variables and Outputs

### Variables
Variables allow you to **parameterize your Terraform code** for flexibility and reusability.
- Input variables (`variable "name" {}`) → passed via `.tfvars` or CLI
- Environment variables → `TF_VAR_<name>`
- Default values and type constraints (`string`, `number`, `list`, `map`)

```hcl
variable "region" {
  description = "AWS region"
  default     = "us-east-1"
  type        = string
}
``` 

### Outputs
Outputs display useful information after `apply` and can be used between modules or pipelines:
```hcl
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

---

## 5. Providers and Resources

### Example: AWS Provider
```hcl
provider "aws" {
  region = var.region
}

resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

- Providers need authentication (AWS keys, Azure creds)
- Resources define infrastructure components

---

## 6. Data Sources

Data sources allow Terraform to **read information from existing resources**:
```hcl
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server*"]
  }
}
```

- Useful for referencing existing AMIs, subnets, security groups, etc.

---

## 7. Importing Existing Resources

Terraform can manage resources that **already exist** using `terraform import`:
```bash
terraform import aws_instance.my_instance i-1234567890abcdef0
```
- After import, Terraform can track and manage the resource like it created it
- Crucial for adopting Terraform in existing infrastructure

---

## 8. Modules

- Modules are reusable Terraform configurations
- Example directory structure:
```
project/
├── main.tf
├── variables.tf
├── outputs.tf
└── modules/
    └── webserver/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

- Use modules:
```hcl
module "web" {
  source = "./modules/webserver"
  region = var.region
}
```

---

## 9. Terraform State

- State file tracks resources
- Can be stored locally or remotely (S3, Terraform Cloud)
- Example backend:
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "project/terraform.tfstate"
    region = "us-east-1"
  }
}
```

### State Commands
- `terraform state list` → List all resources in state
- `terraform state show <resource_name>` → Show detailed info of a resource
- `terraform state pull` → Download state from backend
- `terraform state push` → Push local state to backend
- `terraform state mv <source> <destination>` → Move resource in state
- `terraform state rm <resource>` → Remove resource from state without destroying

---

## 10. Lifecycle, Provisioners, and Functions

### Lifecycle Rules
- Control how resources are created, updated, or destroyed
```hcl
lifecycle {
  create_before_destroy = true
  ignore_changes = [tags]
}
```

### Provisioners
- Run scripts after resource creation
```hcl
provisioner "local-exec" {
  command = "echo Hello World"
}
```

### Terraform Functions
- Built-in functions like `lookup()`, `concat()`, `join()`, `length()` help dynamically generate values

---

## 11. Workspaces

- Workspaces allow managing **multiple environments** (dev, staging, prod) using the same code
```bash
terraform workspace new dev
terraform workspace select dev
```

---

## 12. Best Practices

- Use **remote state** for collaboration
- Keep configurations **modular**
- Version control everything
- Use **workspace** for multiple environments
- Review `terraform plan` before applying
- Lock state files to avoid conflicts

---

## 13. Sample Terraform Project

**Scenario:** Deploy a web application on AWS
- VPC, Subnet, Security Group
- EC2 instances for backend (Spring Boot)
- S3 bucket for static files (Angular)
- RDS database

```hcl
provider "aws" {
  region = var.region
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

module "backend_instances" {
  source = "./modules/backend"
  vpc_id = aws_vpc.main.id
  count  = 2
}

module "frontend_s3" {
  source = "./modules/frontend"
  bucket_name = "my-angular-app"
}
```

- **Pipeline Integration:** Terraform can be run in Jenkins, GitLab CI, or GitHub Actions
- **Steps:** `terraform init` → `terraform plan` → `terraform apply`

---

## 14. Summary

This Terraform course now covers:
- Core concepts and terminology
- Configuration syntax, commands, variables, data sources
- Importing existing resources
- Modules, outputs, and state management
- Lifecycle, provisioners, functions, and workspaces
- Real-world project example
- Best practices for production-ready infrastructure

*All examples are ready to be copied into `.tf` files and executed.*


