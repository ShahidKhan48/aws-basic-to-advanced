# ECR - Docker Images & Private Registry

## 📦 Amazon ECR Overview

### What is ECR?
- **Definition**: Fully managed Docker container registry
- **Purpose**: Store, manage, and deploy Docker container images
- **Integration**: Native integration with ECS, EKS, Lambda
- **Security**: Image scanning, encryption, IAM integration

## 🐳 Docker Images in ECR

### Image Storage
- **Repositories**: Named collections of Docker images
- **Tags**: Version identifiers for images (latest, v1.0, etc.)
- **Layers**: Images stored as layers for efficiency
- **Deduplication**: Shared layers stored once

### Image Lifecycle
1. **Build**: Create Docker image locally
2. **Tag**: Tag image with ECR repository URI
3. **Push**: Upload image to ECR repository
4. **Pull**: Download image for deployment
5. **Deploy**: Use image in ECS, EKS, or other services

### ECR Repository URI Format
```
<account-id>.dkr.ecr.<region>.amazonaws.com/<repository-name>:<tag>

Example:
123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:latest
```

## 🏢 Private Registry Features

### Repository Types
- **Private**: Default, requires authentication
- **Public**: Amazon ECR Public for open-source images
- **Cross-Region**: Replicate images across regions
- **Cross-Account**: Share images between AWS accounts

### Repository Management
- **Naming**: Unique within region and account
- **Permissions**: IAM-based access control
- **Lifecycle Policies**: Automatic image cleanup
- **Replication**: Cross-region and cross-account

### ECR Commands
```bash
# Get login token
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Create repository
aws ecr create-repository --repository-name my-app

# Tag image
docker tag my-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:latest

# Push image
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:latest

# Pull image
docker pull 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:latest
```

## 🔍 Image Scanning

### Vulnerability Scanning
- **Basic Scanning**: Common Vulnerabilities and Exposures (CVE)
- **Enhanced Scanning**: Inspector integration, continuous monitoring
- **Scan Triggers**: On push, scheduled, manual
- **Results**: Detailed vulnerability reports

### Scan Configuration
```json
{
  "scanOnPush": true,
  "scanType": "ENHANCED"
}
```

### Scan Results
- **Critical**: Immediate attention required
- **High**: Important security issues
- **Medium**: Moderate security concerns
- **Low**: Minor security issues
- **Informational**: General information

## 🔐 Security Features

### Encryption
- **At Rest**: AES-256 encryption using AWS KMS
- **In Transit**: HTTPS for all API calls
- **Key Management**: AWS managed or customer managed KMS keys

### Access Control
- **IAM Policies**: Control who can access repositories
- **Resource Policies**: Repository-level permissions
- **Cross-Account**: Share images securely between accounts

### Example IAM Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage",
        "ecr:BatchCheckLayerAvailability"
      ],
      "Resource": "arn:aws:ecr:us-east-1:123456789012:repository/my-app"
    },
    {
      "Effect": "Allow",
      "Action": "ecr:GetAuthorizationToken",
      "Resource": "*"
    }
  ]
}
```

## ♻️ Lifecycle Policies

### What are Lifecycle Policies?
- **Purpose**: Automatically manage image lifecycle
- **Actions**: Delete images based on rules
- **Benefits**: Cost optimization, storage management
- **Rules**: Based on age, count, tags

### Lifecycle Policy Examples

#### Keep Last 10 Images
```json
{
  "rules": [
    {
      "rulePriority": 1,
      "description": "Keep last 10 images",
      "selection": {
        "tagStatus": "any",
        "countType": "imageCountMoreThan",
        "countNumber": 10
      },
      "action": {
        "type": "expire"
      }
    }
  ]
}
```

#### Delete Images Older Than 30 Days
```json
{
  "rules": [
    {
      "rulePriority": 1,
      "description": "Delete images older than 30 days",
      "selection": {
        "tagStatus": "untagged",
        "countType": "sinceImagePushed",
        "countUnit": "days",
        "countNumber": 30
      },
      "action": {
        "type": "expire"
      }
    }
  ]
}
```

## 🌐 Cross-Region Replication

### Replication Configuration
- **Source Region**: Where images are originally stored
- **Destination Regions**: Where images are replicated
- **Filters**: Repository and tag filters
- **Encryption**: Maintain encryption in destination

### Replication Benefits
- **Disaster Recovery**: Images available in multiple regions
- **Performance**: Faster pulls from local region
- **Compliance**: Data residency requirements
- **High Availability**: Reduced single point of failure

### Replication Rule Example
```json
{
  "rules": [
    {
      "destinations": [
        {
          "region": "us-west-2",
          "registryId": "123456789012"
        }
      ],
      "repositoryFilters": [
        {
          "filter": "prod-*",
          "filterType": "PREFIX_MATCH"
        }
      ]
    }
  ]
}
```

## 🔄 Integration with AWS Services

### ECS Integration
- **Task Definitions**: Reference ECR images directly
- **Authentication**: Automatic authentication
- **Pulling**: ECS pulls images during task startup

### EKS Integration
- **Kubernetes Deployments**: Use ECR images in pod specs
- **Image Pull Secrets**: Not required for same-account access
- **Node IAM Roles**: Provide ECR access to worker nodes

### Lambda Integration
- **Container Images**: Deploy Lambda functions as container images
- **Size Limit**: Up to 10GB container images
- **Performance**: Faster cold starts with optimized images

## 💰 Pricing and Cost Optimization

### ECR Pricing
- **Storage**: $0.10 per GB-month
- **Data Transfer**: Standard AWS data transfer rates
- **Requests**: No additional charges for API requests

### Cost Optimization
- **Lifecycle Policies**: Automatically delete old images
- **Image Optimization**: Use multi-stage builds, minimal base images
- **Compression**: Use compressed layers
- **Deduplication**: Leverage layer sharing

## 🎯 Best Practices

### Image Management
- Use semantic versioning for tags
- Implement lifecycle policies
- Scan images for vulnerabilities
- Use minimal base images
- Optimize Dockerfile for layer caching

### Security
- Enable image scanning
- Use IAM for access control
- Encrypt images at rest
- Regular security audits
- Monitor access patterns

### Operations
- Automate image builds and pushes
- Implement CI/CD integration
- Monitor repository metrics
- Set up alerting for scan results
- Regular cleanup of unused images

### Performance
- Use appropriate regions for deployment
- Implement cross-region replication for global applications
- Optimize image sizes
- Use image layer caching
- Monitor pull performance