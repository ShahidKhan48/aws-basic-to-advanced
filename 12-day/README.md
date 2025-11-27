# Day 12: AWS CodeCommit

## Overview
Today we'll explore AWS CodeCommit, Amazon's fully managed source control service that hosts secure Git-based repositories. Learn how to set up repositories, manage access control, and implement collaboration workflows.

## Learning Objectives
- Understand AWS CodeCommit and its benefits
- Learn how to create and configure CodeCommit repositories
- Master Git operations with CodeCommit
- Implement access control and security best practices
- Set up team collaboration workflows

## Topics Covered

### 1. What is AWS CodeCommit?
AWS CodeCommit is a fully managed source control service that hosts secure Git-based repositories. It eliminates the need to operate your own source control system or worry about scaling its infrastructure.

#### Key Features:
- **Fully Managed**: No infrastructure to manage
- **Secure**: Encrypted in transit and at rest
- **Scalable**: Handles repositories of any size
- **Integrated**: Works seamlessly with other AWS services
- **Cost-Effective**: Pay only for what you use

### 2. Benefits of CodeCommit
- **High Availability**: Built on AWS's proven infrastructure
- **Security**: IAM integration for fine-grained access control
- **Collaboration**: Support for pull requests and code reviews
- **Integration**: Native integration with AWS DevOps tools
- **No Size Limits**: Store repositories of any size

### 3. Setting Up CodeCommit

#### Prerequisites:
- AWS account with appropriate permissions
- Git installed on your local machine
- AWS CLI configured

#### Creating a Repository:
```bash
# Using AWS CLI
aws codecommit create-repository \
  --repository-name my-repo \
  --repository-description "My first CodeCommit repository"

# Using AWS Console
# Navigate to CodeCommit → Repositories → Create repository
```

### 4. Authentication Methods

#### HTTPS with Git Credentials:
1. Create Git credentials in IAM console
2. Use username/password for Git operations

#### HTTPS with AWS CLI Credential Helper:
```bash
# Configure Git to use AWS CLI credential helper
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```

#### SSH with Key Pairs:
1. Generate SSH key pair
2. Upload public key to IAM
3. Configure SSH config file

### 5. Basic Git Operations with CodeCommit

#### Clone Repository:
```bash
# HTTPS
git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-repo

# SSH
git clone ssh://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-repo
```

#### Basic Git Workflow:
```bash
# Add files
echo "# My Project" > README.md
git add README.md

# Commit changes
git commit -m "Initial commit"

# Push to CodeCommit
git push origin main

# Pull latest changes
git pull origin main

# Create and switch to new branch
git checkout -b feature-branch

# Push new branch
git push origin feature-branch
```

### 6. Access Control and Security

#### IAM Policies for CodeCommit:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:GitPull",
        "codecommit:GitPush"
      ],
      "Resource": "arn:aws:codecommit:us-east-1:123456789012:my-repo"
    }
  ]
}
```

#### Repository-Level Permissions:
- **Full Access**: All operations on repository
- **Read Only**: Clone and pull operations only
- **Push/Pull**: Push and pull but no admin operations

#### Branch-Level Permissions:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "codecommit:GitPush",
        "codecommit:DeleteBranch",
        "codecommit:PutFile",
        "codecommit:MergeBranchesByFastForward"
      ],
      "Resource": "arn:aws:codecommit:us-east-1:123456789012:my-repo",
      "Condition": {
        "StringEquals": {
          "codecommit:References": [
            "refs/heads/main",
            "refs/heads/prod"
          ]
        }
      }
    }
  ]
}
```

### 7. Collaboration Features

#### Pull Requests:
1. Create feature branch
2. Make changes and push
3. Create pull request in AWS Console
4. Review and approve changes
5. Merge to main branch

#### Code Reviews:
- Line-by-line commenting
- Approval workflows
- Integration with notifications

#### Triggers:
```bash
# Create trigger for Lambda function
aws codecommit put-repository-triggers \
  --repository-name my-repo \
  --triggers repositoryName=my-repo,destinationArn=arn:aws:lambda:us-east-1:123456789012:function:my-function,events=all
```

### 8. Integration with Other AWS Services

#### CodePipeline Integration:
```yaml
Source:
  Provider: AWS CodeCommit
  Configuration:
    RepositoryName: my-repo
    BranchName: main
```

#### CodeBuild Integration:
```yaml
source:
  type: CODECOMMIT
  location: https://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-repo
```

#### Lambda Triggers:
- Trigger Lambda functions on repository events
- Automate workflows based on commits
- Send notifications on code changes

### 9. Best Practices

#### Repository Organization:
- Use meaningful repository names
- Organize repositories by project or team
- Implement consistent branching strategy

#### Branch Strategy:
```
main (production)
├── develop (integration)
├── feature/user-authentication
├── feature/payment-system
└── hotfix/critical-bug-fix
```

#### Commit Messages:
```
feat: add user authentication system
fix: resolve payment processing bug
docs: update API documentation
refactor: optimize database queries
```

#### Security:
- Use IAM roles instead of users when possible
- Implement least privilege access
- Enable MFA for sensitive operations
- Regularly rotate Git credentials

## Hands-on Project: Team Repository Setup

Set up a CodeCommit repository for a team project with proper access control and collaboration workflows.

### Project Requirements:
1. Create a repository for a web application
2. Set up different access levels for team members
3. Implement branch protection for main branch
4. Configure pull request workflow
5. Set up notifications for code changes

### Implementation Steps:

#### Step 1: Create Repository
```bash
aws codecommit create-repository \
  --repository-name team-web-app \
  --repository-description "Team web application repository"
```

#### Step 2: Set Up Access Control
```bash
# Create IAM group for developers
aws iam create-group --group-name CodeCommitDevelopers

# Attach policy to group
aws iam attach-group-policy \
  --group-name CodeCommitDevelopers \
  --policy-arn arn:aws:iam::aws:policy/AWSCodeCommitPowerUser
```

#### Step 3: Configure Branch Protection
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "codecommit:GitPush",
        "codecommit:DeleteBranch"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "codecommit:References": "refs/heads/main"
        },
        "Null": {
          "codecommit:PullRequestId": "true"
        }
      }
    }
  ]
}
```

#### Step 4: Set Up Project Structure
```
team-web-app/
├── README.md
├── .gitignore
├── src/
│   ├── index.html
│   ├── css/
│   └── js/
├── tests/
├── docs/
└── .github/
    └── pull_request_template.md
```

## Hands-on Activities

### Activity 1: Repository Setup
1. Create a new CodeCommit repository
2. Clone it to your local machine
3. Add initial project files
4. Push to repository

### Activity 2: Collaboration Workflow
1. Create a feature branch
2. Make changes and commit
3. Push branch to CodeCommit
4. Create pull request
5. Review and merge changes

### Activity 3: Access Control
1. Create IAM users for team members
2. Set up different permission levels
3. Test access with different users
4. Implement branch protection

## Monitoring and Troubleshooting

#### CloudWatch Metrics:
- Repository size
- Number of commits
- Pull request metrics

#### Common Issues:
- **Authentication failures**: Check Git credentials or SSH keys
- **Permission denied**: Verify IAM permissions
- **Large file issues**: Use Git LFS for large files
- **Merge conflicts**: Resolve conflicts locally before pushing

## Next Steps
Tomorrow we'll explore AWS CodePipeline to create automated CI/CD pipelines using our CodeCommit repositories as source.

## Additional Resources
- [CodeCommit User Guide](https://docs.aws.amazon.com/codecommit/latest/userguide/)
- [Git Documentation](https://git-scm.com/doc)
- [CodeCommit API Reference](https://docs.aws.amazon.com/codecommit/latest/APIReference/)