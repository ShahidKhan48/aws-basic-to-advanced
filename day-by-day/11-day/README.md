# Day 11: AWS CloudFormation

## Overview
Today we'll dive into AWS CloudFormation, Amazon's Infrastructure as Code (IaC) service that allows you to model and provision AWS resources using templates.

## Learning Objectives
- Understand Infrastructure as Code (IaC) concepts
- Learn CloudFormation fundamentals and benefits
- Master CloudFormation template structure
- Create and manage CloudFormation stacks
- Implement best practices for CloudFormation

## Topics Covered

### 1. What is Infrastructure as Code (IaC)?
Infrastructure as Code is the practice of managing and provisioning computing infrastructure through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools.

#### Benefits of IaC:
- **Consistency**: Same infrastructure every time
- **Version Control**: Track changes over time
- **Automation**: Reduce manual errors
- **Scalability**: Easy to replicate environments
- **Cost Management**: Better resource tracking

### 2. AWS CloudFormation Overview
CloudFormation is AWS's native IaC service that allows you to:
- Model your infrastructure in JSON or YAML
- Provision resources in a predictable manner
- Manage the entire lifecycle of your infrastructure
- Handle dependencies automatically

### 3. CloudFormation Template Structure

#### Basic Template Components:
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Template description'

Parameters:
  # Input parameters

Mappings:
  # Static lookup tables

Conditions:
  # Conditional logic

Resources:
  # AWS resources to create

Outputs:
  # Values to return
```

#### Example Template:
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Simple EC2 instance with security group'

Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
    Description: EC2 instance type

  KeyName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: Name of existing EC2 KeyPair

Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security group for EC2 instance
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      ImageId: ami-0abcdef1234567890  # Amazon Linux 2
      SecurityGroupIds:
        - !Ref MySecurityGroup
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd
          systemctl enable httpd
          echo "<h1>Hello from CloudFormation!</h1>" > /var/www/html/index.html

Outputs:
  InstanceId:
    Description: Instance ID of the EC2 instance
    Value: !Ref MyEC2Instance
    Export:
      Name: !Sub "${AWS::StackName}-InstanceId"

  PublicIP:
    Description: Public IP address of the EC2 instance
    Value: !GetAtt MyEC2Instance.PublicIp
```

### 4. CloudFormation Intrinsic Functions

#### Common Functions:
```yaml
# Reference a parameter or resource
!Ref MyParameter

# Get attribute from a resource
!GetAtt MyResource.AttributeName

# Substitute variables
!Sub "Hello ${AWS::Region}"

# Join values
!Join [",", ["a", "b", "c"]]

# Select from a list
!Select [0, ["first", "second", "third"]]

# Base64 encode
!Base64 "string to encode"

# Conditional
!If [ConditionName, ValueIfTrue, ValueIfFalse]
```

### 5. Stack Management

#### Creating a Stack:
```bash
# Using AWS CLI
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml \
  --parameters ParameterKey=KeyName,ParameterValue=my-key

# Using AWS Console
# Navigate to CloudFormation → Create Stack → Upload template
```

#### Updating a Stack:
```bash
aws cloudformation update-stack \
  --stack-name my-stack \
  --template-body file://updated-template.yaml
```

#### Deleting a Stack:
```bash
aws cloudformation delete-stack --stack-name my-stack
```

#### Stack Status:
```bash
aws cloudformation describe-stacks --stack-name my-stack
```

### 6. Advanced CloudFormation Features

#### Nested Stacks:
```yaml
Resources:
  NetworkStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/bucket/network-template.yaml
      Parameters:
        VpcCidr: 10.0.0.0/16
```

#### Stack Sets:
Deploy stacks across multiple accounts and regions.

#### Change Sets:
Preview changes before applying them:
```bash
aws cloudformation create-change-set \
  --stack-name my-stack \
  --change-set-name my-changes \
  --template-body file://template.yaml
```

### 7. CloudFormation Best Practices

#### Template Organization:
- Use nested stacks for complex infrastructures
- Separate templates by lifecycle (network, security, applications)
- Use cross-stack references for shared resources

#### Parameter Management:
- Use parameter files for different environments
- Validate parameter values with constraints
- Provide meaningful descriptions

#### Resource Naming:
- Use consistent naming conventions
- Include environment and purpose in names
- Use logical IDs that describe the resource

#### Security:
- Use IAM roles for CloudFormation
- Follow least privilege principle
- Don't hardcode sensitive values

## Hands-on Project: Complete Application Stack

Create a CloudFormation template that provisions:
- VPC with public and private subnets
- Internet Gateway and NAT Gateway
- Security Groups
- EC2 instances in different subnets
- Application Load Balancer
- RDS database in private subnet

### Project Structure:
```
cloudformation-project/
├── main-template.yaml
├── network-template.yaml
├── security-template.yaml
├── compute-template.yaml
└── database-template.yaml
```

### Main Template Example:
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Complete application stack'

Parameters:
  Environment:
    Type: String
    Default: dev
    AllowedValues: [dev, staging, prod]

Resources:
  NetworkStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub 'https://s3.amazonaws.com/my-bucket/network-template.yaml'
      Parameters:
        Environment: !Ref Environment

  SecurityStack:
    Type: AWS::CloudFormation::Stack
    DependsOn: NetworkStack
    Properties:
      TemplateURL: !Sub 'https://s3.amazonaws.com/my-bucket/security-template.yaml'
      Parameters:
        VpcId: !GetAtt NetworkStack.Outputs.VpcId

  ComputeStack:
    Type: AWS::CloudFormation::Stack
    DependsOn: SecurityStack
    Properties:
      TemplateURL: !Sub 'https://s3.amazonaws.com/my-bucket/compute-template.yaml'
      Parameters:
        VpcId: !GetAtt NetworkStack.Outputs.VpcId
        SecurityGroupId: !GetAtt SecurityStack.Outputs.WebSecurityGroupId
```

## Hands-on Activities

### Activity 1: Basic Template
1. Create a simple CloudFormation template for an EC2 instance
2. Deploy the stack using AWS CLI
3. Verify resources in AWS Console
4. Delete the stack

### Activity 2: Parameterized Template
1. Add parameters for instance type and key pair
2. Use conditions for different environments
3. Add outputs for important values

### Activity 3: Complex Stack
1. Create a multi-tier application stack
2. Use nested stacks for organization
3. Implement proper dependencies
4. Test stack updates

## Troubleshooting Common Issues
- **Stack rollback**: Check CloudFormation events for error details
- **Resource conflicts**: Ensure unique resource names
- **Permission errors**: Verify IAM permissions
- **Template validation**: Use `aws cloudformation validate-template`

## Next Steps
Tomorrow we'll explore AWS CodeCommit for source control, which will be essential for managing our CloudFormation templates in a team environment.

## Additional Resources
- [CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)
- [CloudFormation Template Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html)
- [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)