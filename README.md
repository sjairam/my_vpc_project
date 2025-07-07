# my_vpc_project

This project provisions an AWS Virtual Private Cloud (VPC) using Terraform. It leverages the [terraform-aws-modules/vpc/aws](https://github.com/terraform-aws-modules/terraform-aws-vpc) module to create a VPC with public and private subnets, NAT gateway, and subnet tagging for Kubernetes (EKS) compatibility.

## Features
- Creates a VPC with configurable CIDR block
- Public and private subnets across all availability zones in the selected region
- NAT Gateway for outbound internet access from private subnets
- DNS hostnames enabled
- Subnet and VPC tagging for EKS compatibility

## Prerequisites
- [Terraform](https://www.terraform.io/downloads.html) >= 1.0.0
- AWS credentials configured (via environment variables, AWS CLI, or similar)

## Usage

1. **Clone the repository:**
   ```sh
   git clone <repo-url>
   cd my_vpc_project
   ```
2. **Configure variables:**
   Edit `terraform.tfvars` to set your desired values:
   ```hcl
   region = "us-east-2"
   name   = "acme-test-cluster"
   ```
   Optionally, override defaults for VPC CIDR and subnets in `vpc/variables.tf`.

3. **Initialize and apply Terraform:**
   ```sh
   terraform init
   terraform apply
   ```

## Module Structure
- `main.tf`: Root module, invokes the VPC submodule
- `provider.tf`: AWS provider configuration
- `terraform.tfvars`: Example variable values
- `vpc/`: Contains the VPC submodule
  - `main.tf`: VPC resource definitions
  - `variables.tf`: Input variables for the VPC
  - `locals.tf`: Local values for EKS cluster name
  - `versions.tf`: Required Terraform and provider versions

## Input Variables
| Name              | Description                                 | Type    | Default                                      |
|-------------------|---------------------------------------------|---------|----------------------------------------------|
| region            | AWS region                                  | string  | n/a (set in `terraform.tfvars`)              |
| name              | Name for VPC and EKS cluster                | string  | n/a (set in `terraform.tfvars`)              |
| eks_cluster_name  | EKS cluster name (for subnet tagging)       | string  | "" (defaults to `name` if not set)           |
| cidr              | VPC CIDR block                              | string  | "192.168.0.0/16"                            |
| private_subnets   | List of private subnet CIDRs                | list    | ["192.168.160.0/19", "192.168.128.0/19", "192.168.96.0/19"] |
| public_subnets    | List of public subnet CIDRs                 | list    | ["192.168.64.0/19", "192.168.32.0/19", "192.168.0.0/19"]   |

## Outputs
- VPC and subnet IDs (see the [terraform-aws-vpc module outputs](https://github.com/terraform-aws-modules/terraform-aws-vpc#outputs))

## Example
```hcl
region = "us-east-2"
name   = "acme-test-cluster"
```

## License
MIT
