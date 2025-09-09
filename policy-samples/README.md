# IAM Policy Samples

This directory contains IAM policy templates for AWS services used in the Terraform Enterprise Examples.

## Policy Categories

- **ec2-policies/**: EC2 service policies
- **s3-policies/**: S3 bucket policies
- **eks-policies/**: EKS service policies
- **rds-policies/**: RDS service policies

## Usage

These policy samples follow the principle of least privilege and can be used as:
- Starting points for custom IAM policies
- Reference implementations for security best practices
- Templates for role-based access control

## Policy Structure

Each policy directory contains:
- `policy.json` - The IAM policy document
- `trust-policy.json` - Trust relationships (for roles)
- `README.md` - Documentation and usage examples
- `variables.tf` - Terraform variables for customization

## Security Best Practices

### 1. Least Privilege Principle
- Grant only the minimum permissions required
- Use specific resource ARNs when possible
- Avoid wildcard permissions in production

### 2. Conditional Access
- Use condition blocks for additional security
- Implement time-based restrictions where appropriate
- Add IP-based restrictions for sensitive operations

### 3. Regular Review
- Audit policies regularly
- Remove unused permissions
- Update policies when requirements change

## Example Policy Usage in Terraform

```hcl
# Using a policy template
data "aws_iam_policy_document" "ec2_policy" {
  source_json = file("${path.module}/policy-samples/ec2-policies/policy.json")
}

# Creating an IAM role with the policy
resource "aws_iam_role" "ec2_role" {
  name = "ec2-service-role"
  assume_role_policy = file("${path.module}/policy-samples/ec2-policies/trust-policy.json")
}

resource "aws_iam_policy" "ec2_policy" {
  name   = "ec2-service-policy"
  policy = data.aws_iam_policy_document.ec2_policy.json
}

resource "aws_iam_role_policy_attachment" "ec2_attachment" {
  role       = aws_iam_role.ec2_role.name
  policy_arn = aws_iam_policy.ec2_policy.arn
}
```

## Policy Testing

Before deploying policies:

1. Use AWS IAM Policy Simulator
2. Test with least privileged test users
3. Validate against AWS Config rules
4. Review with security team

## Contributing

When contributing new policies:

1. Follow AWS IAM best practices
2. Include comprehensive documentation
3. Test policies thoroughly
4. Use meaningful resource names
5. Add usage examples
6. Include any necessary conditions

## Common Policy Patterns

### Service-Specific Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "service:SpecificAction"
      ],
      "Resource": [
        "arn:aws:service:region:account:resource/specific-resource"
      ]
    }
  ]
}
```

### Cross-Account Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::TRUSTED-ACCOUNT:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "unique-external-id"
        }
      }
    }
  ]
}
```

## Important Notes

- Always validate policies before production deployment
- Use AWS CloudTrail to monitor policy usage
- Implement policy versioning for change management
- Document any custom conditions or constraints

## Support

For questions about specific policies:
1. Check the individual policy README files
2. Review AWS IAM documentation
3. Use AWS support for policy validation
4. Open an issue for policy-specific questions
