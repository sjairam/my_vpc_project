# AWS VPC Terraform Module

This project provisions an AWS Virtual Private Cloud (VPC) using Terraform. It leverages the [terraform-aws-modules/vpc/aws](https://github.com/terraform-aws-modules/terraform-aws-vpc) module to create a VPC with public and private subnets, NAT gateway, and subnet tagging for Kubernetes (EKS) compatibility.

## 🏗️ Architecture

The VPC is designed with the following components:
- **VPC**: Main virtual private cloud with configurable CIDR block
- **Public Subnets**: Subnets with direct internet access via Internet Gateway
- **Private Subnets**: Subnets with outbound internet access via NAT Gateway
- **NAT Gateway**: Single NAT Gateway for cost optimization
- **Internet Gateway**: Provides internet connectivity to public subnets
- **Route Tables**: Separate routing for public and private subnets
- **EKS Tags**: Proper tagging for Kubernetes cluster integration

```
┌─────────────────────────────────────────────────────────────┐
│                        AWS VPC                              │
│  ┌─────────────────┐    ┌─────────────────┐                │
│  │   Public Subnet │    │  Private Subnet │                │
│  │   (AZ-1)        │    │   (AZ-1)        │                │
│  │                 │    │                 │                │
│  │ ┌─────────────┐ │    │ ┌─────────────┐ │                │
│  │ │   EC2/ELB   │ │    │ │   EKS Node  │ │                │
│  │ │             │ │    │ │             │ │                │
│  │ └─────────────┘ │    │ └─────────────┘ │                │
│  └─────────────────┘    └─────────────────┘                │
│           │                       │                        │
│           │                       │                        │
│  ┌─────────────────┐    ┌─────────────────┐                │
│  │   Public Subnet │    │  Private Subnet │                │
│  │   (AZ-2)        │    │   (AZ-2)        │                │
│  └─────────────────┘    └─────────────────┘                │
│           │                       │                        │
│           │                       │                        │
│  ┌─────────────────┐    ┌─────────────────┐                │
│  │   Public Subnet │    │  Private Subnet │                │
│  │   (AZ-3)        │    │   (AZ-3)        │                │
│  └─────────────────┘    └─────────────────┘                │
│           │                       │                        │
│           ▼                       ▼                        │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Internet Gateway                       │    │
│  └─────────────────────────────────────────────────────┘    │
│           │                                                 │
│           ▼                                                 │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              NAT Gateway                            │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

## ✨ Features

- 🏢 **Multi-AZ Deployment**: Creates subnets across all availability zones in the selected region
- 🌐 **Internet Connectivity**: Public subnets with direct internet access via Internet Gateway
- 🔒 **Private Networking**: Private subnets with controlled outbound access via NAT Gateway
- 💰 **Cost Optimization**: Single NAT Gateway to minimize costs
- 🏷️ **EKS Ready**: Proper tagging for Kubernetes cluster integration
- 🔧 **DNS Support**: DNS hostnames enabled for better service discovery
- 📊 **Monitoring Ready**: Comprehensive tagging for resource management

## 📋 Prerequisites

- [Terraform](https://www.terraform.io/downloads.html) >= 1.0.0
- AWS CLI configured with appropriate credentials
- AWS credentials configured (via environment variables, AWS CLI, or IAM roles)

### AWS Permissions Required

Your AWS credentials should have the following permissions:
- `ec2:*` - For VPC, subnet, and networking resources
- `iam:*` - For NAT Gateway and related IAM resources
- `elasticloadbalancing:*` - For load balancer integration

## 🚀 Quick Start

1. **Clone the repository:**
   ```bash
   git clone <repo-url>
   cd my_vpc_project
   ```

2. **Configure variables:**
   Create a `terraform.tfvars` file with your desired values:
   ```hcl
   region = "us-east-2"
   name   = "my-production-vpc"
   ```

3. **Initialize Terraform:**
   ```bash
   terraform init
   ```

4. **Review the plan:**
   ```bash
   terraform plan
   ```

5. **Apply the configuration:**
   ```bash
   terraform apply
   ```

## 📁 Module Structure

```
my_vpc_project/
├── main.tf              # Root module configuration
├── provider.tf          # AWS provider configuration
├── README.md           # This file
├── terraform.tfvars    # Variable values (create this)
└── vpc/                # VPC submodule
    ├── main.tf         # VPC resource definitions
    ├── variables.tf    # Input variables
    ├── locals.tf       # Local values
    └── versions.tf     # Terraform version constraints
```

## ⚙️ Configuration

### Input Variables

| Variable | Description | Type | Default | Required |
|----------|-------------|------|---------|----------|
| `region` | AWS region for resource deployment | `string` | - | ✅ |
| `name` | Name prefix for VPC and resources | `string` | - | ✅ |
| `eks_cluster_name` | EKS cluster name for subnet tagging | `string` | `""` | ❌ |
| `cidr` | VPC CIDR block | `string` | `"192.168.0.0/16"` | ❌ |
| `private_subnets` | List of private subnet CIDRs | `list(string)` | `["192.168.160.0/19", "192.168.128.0/19", "192.168.96.0/19"]` | ❌ |
| `public_subnets` | List of public subnet CIDRs | `list(string)` | `["192.168.64.0/19", "192.168.32.0/19", "192.168.0/19"]` | ❌ |

### Example Configurations

**Basic VPC:**
```hcl
region = "us-east-2"
name   = "my-vpc"
```

**Custom CIDR Configuration:**
```hcl
region = "us-west-2"
name   = "production-vpc"
cidr   = "10.0.0.0/16"
private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
```

**EKS-Optimized Configuration:**
```hcl
region = "us-east-1"
name   = "eks-cluster-vpc"
eks_cluster_name = "my-eks-cluster"
```

## 📤 Outputs

The module provides the following outputs from the underlying VPC module:

| Output | Description |
|--------|-------------|
| `vpc_id` | The ID of the VPC |
| `vpc_cidr_block` | The CIDR block of the VPC |
| `private_subnets` | List of IDs of private subnets |
| `public_subnets` | List of IDs of public subnets |
| `private_subnet_arns` | List of ARNs of private subnets |
| `public_subnet_arns` | List of ARNs of public subnets |
| `nat_gateway_ids` | List of NAT Gateway IDs |
| `nat_public_ips` | List of public Elastic IPs created for NAT Gateway |
| `igw_id` | The ID of the Internet Gateway |

## 🔒 Security Considerations

- **Network ACLs**: Consider implementing custom Network ACLs for additional security
- **Security Groups**: Define appropriate security groups for your workloads
- **VPC Flow Logs**: Enable VPC Flow Logs for network monitoring and security analysis
- **Private Subnets**: Use private subnets for sensitive workloads
- **NAT Gateway**: Consider using NAT Instances for cost-sensitive environments

## 🛠️ Troubleshooting

### Common Issues

**Error: "No valid credential sources found"**
```bash
# Configure AWS credentials
aws configure
# Or set environment variables
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_REGION="us-east-2"
```

**Error: "VPC CIDR block conflicts"**
- Ensure your VPC CIDR doesn't overlap with existing VPCs
- Use a different CIDR range (e.g., `10.0.0.0/16`, `172.16.0.0/16`)

**Error: "Insufficient IP addresses in subnet"**
- Increase the subnet size by using a smaller CIDR (e.g., `/24` instead of `/25`)
- Ensure you have enough IP addresses for your workloads

### Useful Commands

```bash
# View current state
terraform show

# List all resources
terraform state list

# Refresh state
terraform refresh

# Destroy resources
terraform destroy
```

## 🔄 Maintenance

### Updating the VPC Module

To update to a newer version of the terraform-aws-vpc module:

1. Update the version in `vpc/main.tf`:
   ```hcl
   version = "5.8.1"  # Change to desired version
   ```

2. Run terraform plan to see changes:
   ```bash
   terraform plan
   ```

3. Apply the changes:
   ```bash
   terraform apply
   ```

### Backup and Recovery

- Use Terraform state management (Terraform Cloud, S3 backend)
- Regularly backup your `terraform.tfstate` file
- Document any manual changes made outside of Terraform

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📞 Support

For issues and questions:
- Create an issue in the repository
- Check the [terraform-aws-vpc module documentation](https://github.com/terraform-aws-modules/terraform-aws-vpc)
- Review AWS VPC documentation

## 🔗 Related Resources

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [terraform-aws-vpc Module](https://github.com/terraform-aws-modules/terraform-aws-vpc)
- [EKS Best Practices](https://docs.aws.amazon.com/eks/latest/userguide/best-practices-vpc.html)
