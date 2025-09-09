# AWS Terraform Enterprise Examples

## Overview

This repository provides enterprise-grade AWS infrastructure modules and patterns using Terraform, following best practices from terraform-aws-modules. It includes production-ready modules for VPC networking, EKS clusters, RDS databases, IAM policies, S3 buckets, and disaster recovery patterns designed for enterprise-scale deployments.

Whether you're building a new cloud infrastructure or migrating existing workloads to AWS, these modules provide the foundation for secure, scalable, and maintainable infrastructure as code solutions.

## Features

### 🚀 **VPC Networking Module**
- Multi-AZ VPC with public and private subnets
- NAT Gateways and Internet Gateway configuration
- Route tables with custom routing
- Network ACLs and security groups
- VPC Endpoints for AWS services

### ⚓ **Amazon EKS Module**
- Production-ready EKS cluster configuration
- Managed node groups with auto-scaling
- IRSA (IAM Roles for Service Accounts)
- ALB Ingress Controller setup
- Cluster autoscaler and metrics server
- OIDC provider configuration

### 🗄️ **Amazon RDS Module**
- Multi-AZ RDS deployments
- Automated backup and point-in-time recovery
- Enhanced monitoring and performance insights
- Parameter groups and option groups
- Subnet groups and security configurations
- Read replicas for high availability

### 🔐 **IAM Security Module**
- Role-based access control policies
- Cross-account trust relationships
- Service-linked roles
- Least privilege principle implementation
- Policy attachments and inline policies

### 📦 **S3 Storage Module**
- Bucket policies and lifecycle management
- Server-side encryption configuration
- Versioning and MFA delete protection
- Cross-region replication
- CloudFront distribution integration
- Access logging and monitoring

### 🏥 **Disaster Recovery Patterns**
- Cross-region backup strategies
- Multi-region active-passive setup
- Database failover automation
- Infrastructure replication patterns
- Recovery time optimization

## Directory Structure

```
aws-terraform-enterprise-examples/
├── README.md
├── modules/
│   ├── base/                   # Base infrastructure components
│   │   ├── vpc/               # VPC networking module
│   │   ├── security-groups/   # Security group configurations
│   │   └── iam/              # IAM roles and policies
│   └── eks/                   # EKS cluster module
│       ├── cluster/          # EKS cluster configuration
│       ├── node-groups/      # Managed node group setup
│       └── addons/           # EKS addons and integrations
├── examples/                  # Usage examples and demos
│   ├── complete-stack/       # Full enterprise stack example
│   ├── eks-example/          # EKS deployment example
│   ├── s3-example/           # S3 configuration example
│   └── multi-region/         # Multi-region setup example
└── policy-samples/            # IAM policy templates
    ├── ec2-policies/         # EC2 service policies
    ├── s3-policies/          # S3 bucket policies
    ├── eks-policies/         # EKS service policies
    └── rds-policies/         # RDS service policies
```

## Sample Usage

### EKS Cluster Deployment

```hcl
module "eks_cluster" {
  source = "./modules/eks/cluster"

  cluster_name    = "production-eks"
  cluster_version = "1.28"
  
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  
  # Node groups configuration
  node_groups = {
    general = {
      desired_capacity = 3
      max_capacity     = 10
      min_capacity     = 3
      
      instance_types = ["t3.medium"]
      capacity_type  = "ON_DEMAND"
      
      k8s_labels = {
        Environment = "production"
        NodeGroup   = "general"
      }
    }
    
    spot_nodes = {
      desired_capacity = 2
      max_capacity     = 5
      min_capacity     = 0
      
      instance_types = ["t3.medium", "t3a.medium"]
      capacity_type  = "SPOT"
      
      k8s_labels = {
        Environment = "production"
        NodeGroup   = "spot"
      }
    }
  }
  
  # Enable IRSA
  enable_irsa = true
  
  # Install essential addons
  cluster_addons = {
    coredns = {
      most_recent = true
    }
    kube-proxy = {
      most_recent = true
    }
    vpc-cni = {
      most_recent = true
    }
    aws-ebs-csi-driver = {
      most_recent = true
    }
  }
  
  tags = {
    Environment = "production"
    Project     = "enterprise-platform"
    Owner       = "platform-team"
  }
}
```

### S3 Bucket with Security

```hcl
module "secure_s3_bucket" {
  source = "./modules/base/s3"
  
  bucket_name = "enterprise-data-lake-prod"
  
  # Enable versioning
  versioning = {
    enabled = true
  }
  
  # Server-side encryption
  server_side_encryption_configuration = {
    rule = {
      apply_server_side_encryption_by_default = {
        kms_master_key_id = aws_kms_key.s3_key.arn
        sse_algorithm     = "aws:kms"
      }
      bucket_key_enabled = true
    }
  }
  
  # Lifecycle configuration
  lifecycle_configuration = {
    rule = {
      id     = "enterprise_lifecycle"
      status = "Enabled"
      
      expiration = {
        days = 90
      }
      
      noncurrent_version_expiration = {
        noncurrent_days = 30
      }
      
      transition = {
        days          = 30
        storage_class = "STANDARD_INFREQUENT_ACCESS"
      }
    }
  }
  
  # Block public access
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
  
  # Enable logging
  logging = {
    target_bucket = "enterprise-access-logs"
    target_prefix = "s3-access-logs/"
  }
  
  tags = {
    Environment = "production"
    DataClass   = "confidential"
    Backup      = "enabled"
  }
}
```

## Getting Started

### Prerequisites

Before using these Terraform modules, ensure you have:

- **Terraform** >= 1.0
- **AWS CLI** configured with appropriate permissions
- **kubectl** (for EKS examples)
- **Helm** >= 3.0 (for Kubernetes deployments)
- **Git** for version control

### Required AWS Permissions

Your AWS credentials should have permissions for:
- VPC and networking resources
- EKS cluster management
- RDS instance creation and management
- S3 bucket operations
- IAM role and policy management
- KMS key operations
- CloudWatch and logging services

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/sandeepkatakam21/aws-terraform-enterprise-examples.git
   cd aws-terraform-enterprise-examples
   ```

2. **Initialize Terraform:**
   ```bash
   terraform init
   ```

3. **Configure your variables:**
   ```bash
   cp terraform.tfvars.example terraform.tfvars
   # Edit terraform.tfvars with your specific values
   ```

4. **Plan your deployment:**
   ```bash
   terraform plan
   ```

5. **Apply the configuration:**
   ```bash
   terraform apply
   ```

### Quick Start Example

```bash
# Deploy a complete VPC with EKS cluster
cd examples/complete-stack
terraform init
terraform plan -var-file="production.tfvars"
terraform apply -var-file="production.tfvars"

# Configure kubectl for your new cluster
aws eks update-kubeconfig --region us-west-2 --name production-eks

# Verify cluster access
kubectl get nodes
```

## Contribution Guidelines

We welcome contributions to improve and extend these Terraform modules! Here's how you can contribute:

### How to Contribute

1. **Fork the repository**
2. **Create a feature branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes and test thoroughly**
4. **Commit your changes:**
   ```bash
   git commit -m "Add your feature description"
   ```
5. **Push to your branch:**
   ```bash
   git push origin feature/your-feature-name
   ```
6. **Create a Pull Request**

### Development Standards

- **Code Quality**: Follow Terraform best practices and use `terraform fmt`
- **Documentation**: Update README files and add inline comments
- **Testing**: Test modules in isolated environments before submitting
- **Security**: Follow AWS security best practices and least privilege principles
- **Versioning**: Use semantic versioning for module releases

### What We're Looking For

- Additional AWS service modules (Lambda, API Gateway, CloudFront)
- Enhanced security configurations
- Cost optimization patterns
- Multi-region deployment examples
- Integration with CI/CD pipelines
- Improved documentation and examples
- Bug fixes and performance improvements

### Code Review Process

1. All changes must be reviewed by at least one maintainer
2. Automated tests must pass
3. Security scan must show no critical issues
4. Documentation must be updated accordingly
5. Examples should be provided for new features

## Support and Community

For questions, issues, and discussions:

- 📧 **Issues**: [Open an issue](https://github.com/sandeepkatakam21/aws-terraform-enterprise-examples/issues)
- 📖 **Documentation**: Check module-specific README files
- 💬 **Discussions**: Join our GitHub Discussions
- 🔗 **Wiki**: Visit our [Wiki](https://github.com/sandeepkatakam21/aws-terraform-enterprise-examples/wiki) for detailed guides

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

### MIT License Summary

- ✅ Commercial use
- ✅ Modification
- ✅ Distribution
- ✅ Private use
- ❌ Liability
- ❌ Warranty

---

**Built with ❤️ for the AWS and Terraform community**

*"Infrastructure as Code is not just about automation; it's about creating reproducible, scalable, and maintainable systems that empower teams to innovate faster."*
