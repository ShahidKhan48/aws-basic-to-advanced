# Day 10: AWS CLI

## Overview
Today we'll learn about the AWS Command Line Interface (CLI), a powerful tool that allows you to interact with AWS services from the command line.

## Learning Objectives
- Understand what AWS CLI is and its benefits
- Learn how to install and configure AWS CLI
- Master basic AWS CLI commands
- Understand AWS CLI profiles and configuration
- Practice common AWS operations using CLI

## Topics Covered

### 1. What is AWS CLI?
The AWS Command Line Interface (CLI) is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts.

### 2. Benefits of AWS CLI
- **Automation**: Script repetitive tasks
- **Efficiency**: Faster than using the console for many operations
- **Integration**: Easy to integrate with CI/CD pipelines
- **Consistency**: Same commands across different environments

### 3. Installation

#### Windows
```bash
# Download and run the MSI installer from AWS website
# Or use pip
pip install awscli
```

#### macOS
```bash
# Using Homebrew
brew install awscli

# Or using pip
pip install awscli
```

#### Linux
```bash
# Using pip
pip install awscli

# Or using package manager
sudo apt-get install awscli  # Ubuntu/Debian
sudo yum install awscli      # CentOS/RHEL
```

### 4. Configuration

#### Basic Configuration
```bash
aws configure
```
This will prompt for:
- AWS Access Key ID
- AWS Secret Access Key
- Default region name
- Default output format (json, yaml, text, table)

#### Configuration Files
- **Credentials**: `~/.aws/credentials`
- **Config**: `~/.aws/config`

### 5. AWS CLI Profiles
```bash
# Create a new profile
aws configure --profile myprofile

# Use a specific profile
aws s3 ls --profile myprofile

# Set default profile
export AWS_PROFILE=myprofile
```

### 6. Common AWS CLI Commands

#### S3 Operations
```bash
# List buckets
aws s3 ls

# Create bucket
aws s3 mb s3://my-bucket-name

# Upload file
aws s3 cp file.txt s3://my-bucket-name/

# Download file
aws s3 cp s3://my-bucket-name/file.txt ./

# Sync directories
aws s3 sync ./local-folder s3://my-bucket-name/folder/
```

#### EC2 Operations
```bash
# List instances
aws ec2 describe-instances

# Start instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Stop instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Create key pair
aws ec2 create-key-pair --key-name MyKeyPair
```

#### IAM Operations
```bash
# List users
aws iam list-users

# Create user
aws iam create-user --user-name newuser

# List policies
aws iam list-policies --scope Local
```

### 7. Output Formats
```bash
# JSON (default)
aws s3 ls --output json

# Table format
aws s3 ls --output table

# Text format
aws s3 ls --output text

# YAML format
aws s3 ls --output yaml
```

### 8. Filtering and Querying
```bash
# Using JMESPath queries
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name]'

# Filter by tags
aws ec2 describe-instances --filters "Name=tag:Environment,Values=Production"
```

## Hands-on Activities

### Activity 1: Setup and Configuration
1. Install AWS CLI on your system
2. Configure AWS CLI with your credentials
3. Test the configuration with `aws sts get-caller-identity`

### Activity 2: S3 Operations
1. Create an S3 bucket using CLI
2. Upload a file to the bucket
3. List bucket contents
4. Download the file to a different location

### Activity 3: EC2 Operations
1. List all EC2 instances in your account
2. Get details of a specific instance
3. Create a new key pair

### Activity 4: Working with Profiles
1. Create a new AWS CLI profile
2. Switch between profiles
3. Use different profiles for different commands

## Best Practices
- **Never hardcode credentials** in scripts
- **Use IAM roles** when running on EC2 instances
- **Use profiles** for different environments
- **Enable MFA** for sensitive operations
- **Use least privilege** principle for IAM policies
- **Regularly rotate** access keys

## Common CLI Options
```bash
# Dry run (for supported commands)
aws ec2 run-instances --dry-run

# Debug output
aws s3 ls --debug

# No verify SSL (not recommended for production)
aws s3 ls --no-verify-ssl

# Endpoint URL (for local testing)
aws s3 ls --endpoint-url http://localhost:4566
```

## Troubleshooting
- **Check credentials**: `aws sts get-caller-identity`
- **Verify region**: `aws configure get region`
- **Check permissions**: Ensure IAM user has required permissions
- **Update CLI**: `pip install --upgrade awscli`

## Next Steps
Tomorrow we'll explore AWS CloudFormation for Infrastructure as Code, where AWS CLI knowledge will be very useful for managing CloudFormation stacks.

## Additional Resources
- [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)
- [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)
- [JMESPath Tutorial](https://jmespath.org/tutorial.html)