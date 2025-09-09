# Usage Examples

This directory contains practical examples demonstrating how to use the AWS Terraform Enterprise modules.

## Examples

- **complete-stack/**: Full enterprise stack example with VPC, EKS, and RDS
- **eks-example/**: EKS deployment example
- **s3-example/**: S3 configuration example with security best practices
- **multi-region/**: Multi-region setup example for disaster recovery

## Getting Started

Each example directory contains:
- `main.tf` - Main Terraform configuration
- `variables.tf` - Input variables
- `outputs.tf` - Output values
- `terraform.tfvars.example` - Sample variable values
- `README.md` - Example-specific documentation

## Prerequisites

Before running any example:

1. Install Terraform >= 1.0
2. Configure AWS CLI with appropriate credentials
3. Copy `terraform.tfvars.example` to `terraform.tfvars` and customize
4. Run `terraform init` to initialize
5. Run `terraform plan` to review changes
6. Run `terraform apply` to deploy

## Example Usage

```bash
# Navigate to an example
cd examples/complete-stack

# Copy and customize variables
cp terraform.tfvars.example terraform.tfvars
# Edit terraform.tfvars with your values

# Initialize and apply
terraform init
terraform plan -var-file="terraform.tfvars"
terraform apply -var-file="terraform.tfvars"
```

## Cost Considerations

⚠️ **Important**: These examples create real AWS resources that may incur costs. Always:
- Review the `terraform plan` output before applying
- Destroy resources when done testing: `terraform destroy`
- Monitor your AWS billing dashboard
- Use appropriate instance sizes for testing vs production

## Contributing

When adding new examples:
1. Follow the standard structure outlined above
2. Include comprehensive documentation
3. Test thoroughly before submitting
4. Add cost estimates where possible
5. Include cleanup instructions
