# EKS Cluster Module

This directory contains Terraform modules for Amazon EKS (Elastic Kubernetes Service) cluster deployment.

## Modules

- **cluster/**: Main EKS cluster configuration
- **node-groups/**: Managed node group setup
- **addons/**: EKS addons and integrations

## Features

- Production-ready EKS cluster configuration
- Managed node groups with auto-scaling
- IRSA (IAM Roles for Service Accounts) support
- ALB Ingress Controller integration
- Cluster autoscaler and metrics server
- OIDC provider configuration
- VPC CNI and EBS CSI driver setup

## Usage

```hcl
module "eks_cluster" {
  source = "./modules/eks/cluster"

  cluster_name    = "my-eks-cluster"
  cluster_version = "1.28"
  
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  
  # Node groups configuration
  node_groups = {
    general = {
      desired_capacity = 3
      max_capacity     = 10
      min_capacity     = 3
      instance_types   = ["t3.medium"]
    }
  }
  
  tags = {
    Environment = "production"
    Project     = "my-project"
  }
}
```

## Requirements

- Terraform >= 1.0
- AWS Provider >= 5.0
- kubectl configured for cluster access
- Helm >= 3.0 (for addon installations)

## Contributing

When modifying EKS modules:
1. Test in a development environment first
2. Ensure backwards compatibility
3. Update documentation and examples
4. Follow AWS EKS best practices
