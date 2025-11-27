# Day 13: AWS CodePipeline

## Overview
Today we'll explore AWS CodePipeline, a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates.

## Learning Objectives
- Understand CI/CD concepts and benefits
- Learn AWS CodePipeline fundamentals
- Create end-to-end CI/CD pipelines
- Integrate multiple AWS services in pipelines
- Implement deployment strategies and best practices

## Topics Covered

### 1. What is CI/CD?

#### Continuous Integration (CI):
- Developers frequently merge code changes
- Automated builds and tests run on each commit
- Early detection of integration issues
- Faster feedback to developers

#### Continuous Delivery (CD):
- Automated deployment to staging environments
- Manual approval for production deployments
- Consistent and reliable releases

#### Continuous Deployment:
- Fully automated deployment to production
- No manual intervention required
- High confidence in automated testing

### 2. AWS CodePipeline Overview
CodePipeline is a continuous delivery service that automates the build, test, and deploy phases of your release process every time there is a code change.

#### Key Features:
- **Visual Workflow**: Easy-to-understand pipeline visualization
- **Flexible**: Support for multiple source providers and deployment targets
- **Integrated**: Native integration with AWS services
- **Scalable**: Handles pipelines of any complexity
- **Secure**: IAM-based access control

### 3. Pipeline Components

#### Source Stage:
- **CodeCommit**: AWS Git repositories
- **GitHub**: Public and private repositories
- **S3**: Versioned objects in S3 buckets
- **Bitbucket**: Atlassian Bitbucket repositories

#### Build Stage:
- **CodeBuild**: AWS managed build service
- **Jenkins**: Popular open-source automation server
- **TeamCity**: JetBrains CI/CD solution

#### Deploy Stage:
- **CodeDeploy**: AWS deployment service
- **CloudFormation**: Infrastructure as Code
- **ECS**: Container deployments
- **S3**: Static website deployments
- **Lambda**: Serverless function deployments

### 4. Creating Your First Pipeline

#### Basic Pipeline Structure:
```yaml
Pipeline:
  - Source Stage
    - Source Action (CodeCommit)
  - Build Stage
    - Build Action (CodeBuild)
  - Deploy Stage
    - Deploy Action (CodeDeploy)
```

#### Using AWS CLI:
```bash
# Create pipeline
aws codepipeline create-pipeline --cli-input-json file://pipeline.json
```

#### Pipeline JSON Configuration:
```json
{
  "pipeline": {
    "name": "MyFirstPipeline",
    "roleArn": "arn:aws:iam::123456789012:role/CodePipelineRole",
    "artifactStore": {
      "type": "S3",
      "location": "my-pipeline-artifacts-bucket"
    },
    "stages": [
      {
        "name": "Source",
        "actions": [
          {
            "name": "SourceAction",
            "actionTypeId": {
              "category": "Source",
              "owner": "AWS",
              "provider": "CodeCommit",
              "version": "1"
            },
            "configuration": {
              "RepositoryName": "my-repo",
              "BranchName": "main"
            },
            "outputArtifacts": [
              {
                "name": "SourceOutput"
              }
            ]
          }
        ]
      },
      {
        "name": "Build",
        "actions": [
          {
            "name": "BuildAction",
            "actionTypeId": {
              "category": "Build",
              "owner": "AWS",
              "provider": "CodeBuild",
              "version": "1"
            },
            "configuration": {
              "ProjectName": "my-build-project"
            },
            "inputArtifacts": [
              {
                "name": "SourceOutput"
              }
            ],
            "outputArtifacts": [
              {
                "name": "BuildOutput"
              }
            ]
          }
        ]
      },
      {
        "name": "Deploy",
        "actions": [
          {
            "name": "DeployAction",
            "actionTypeId": {
              "category": "Deploy",
              "owner": "AWS",
              "provider": "CodeDeploy",
              "version": "1"
            },
            "configuration": {
              "ApplicationName": "my-application",
              "DeploymentGroupName": "my-deployment-group"
            },
            "inputArtifacts": [
              {
                "name": "BuildOutput"
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### 5. Advanced Pipeline Features

#### Parallel Actions:
```json
{
  "name": "ParallelStage",
  "actions": [
    {
      "name": "UnitTests",
      "actionTypeId": {
        "category": "Test",
        "owner": "AWS",
        "provider": "CodeBuild",
        "version": "1"
      },
      "runOrder": 1
    },
    {
      "name": "SecurityScan",
      "actionTypeId": {
        "category": "Test",
        "owner": "AWS",
        "provider": "CodeBuild",
        "version": "1"
      },
      "runOrder": 1
    }
  ]
}
```

#### Manual Approval:
```json
{
  "name": "ManualApproval",
  "actionTypeId": {
    "category": "Approval",
    "owner": "AWS",
    "provider": "Manual",
    "version": "1"
  },
  "configuration": {
    "CustomData": "Please review the changes before deploying to production",
    "NotificationArn": "arn:aws:sns:us-east-1:123456789012:approval-topic"
  }
}
```

#### Cross-Region Deployments:
```json
{
  "name": "DeployToMultipleRegions",
  "actions": [
    {
      "name": "DeployToUSEast1",
      "region": "us-east-1",
      "actionTypeId": {
        "category": "Deploy",
        "owner": "AWS",
        "provider": "CloudFormation",
        "version": "1"
      }
    },
    {
      "name": "DeployToUSWest2",
      "region": "us-west-2",
      "actionTypeId": {
        "category": "Deploy",
        "owner": "AWS",
        "provider": "CloudFormation",
        "version": "1"
      }
    }
  ]
}
```

### 6. Integration with AWS Services

#### CodeBuild Integration:
```yaml
# buildspec.yml
version: 0.2
phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
artifacts:
  files:
    - '**/*'
```

#### CloudFormation Integration:
```json
{
  "name": "CreateStack",
  "actionTypeId": {
    "category": "Deploy",
    "owner": "AWS",
    "provider": "CloudFormation",
    "version": "1"
  },
  "configuration": {
    "ActionMode": "CREATE_UPDATE",
    "StackName": "my-application-stack",
    "TemplatePath": "BuildOutput::template.yaml",
    "Capabilities": "CAPABILITY_IAM",
    "RoleArn": "arn:aws:iam::123456789012:role/CloudFormationRole"
  }
}
```

### 7. Pipeline Monitoring and Troubleshooting

#### CloudWatch Integration:
- Pipeline execution metrics
- Stage duration tracking
- Failure notifications
- Custom dashboards

#### Pipeline History:
```bash
# Get pipeline execution history
aws codepipeline list-pipeline-executions --pipeline-name MyPipeline

# Get execution details
aws codepipeline get-pipeline-execution \
  --pipeline-name MyPipeline \
  --pipeline-execution-id execution-id
```

#### Common Issues:
- **IAM Permission Errors**: Verify service roles
- **Artifact Issues**: Check S3 bucket permissions
- **Build Failures**: Review CodeBuild logs
- **Deployment Failures**: Check target environment

### 8. Best Practices

#### Pipeline Design:
- Keep pipelines simple and focused
- Use parallel actions where possible
- Implement proper error handling
- Include comprehensive testing stages

#### Security:
- Use IAM roles with least privilege
- Encrypt artifacts in S3
- Secure sensitive configuration data
- Implement approval gates for production

#### Performance:
- Optimize build times
- Use caching strategies
- Parallel execution where appropriate
- Monitor pipeline metrics

## Hands-on Project: Complete CI/CD Pipeline

Create a comprehensive CI/CD pipeline for a web application that includes source control, build, test, and deployment stages.

### Project Architecture:
```
CodeCommit → CodeBuild → Test → Manual Approval → Deploy to Staging → Deploy to Production
```

### Implementation Steps:

#### Step 1: Prepare Source Repository
```bash
# Create repository structure
mkdir my-web-app
cd my-web-app

# Create application files
cat > index.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>My Web App</title>
</head>
<body>
    <h1>Hello from CI/CD Pipeline!</h1>
    <p>Version: 1.0.0</p>
</body>
</html>
EOF

# Create buildspec
cat > buildspec.yml << EOF
version: 0.2
phases:
  pre_build:
    commands:
      - echo Pre-build phase started on \`date\`
  build:
    commands:
      - echo Build phase started on \`date\`
      - echo Running tests...
      - # Add your test commands here
  post_build:
    commands:
      - echo Post-build phase completed on \`date\`
artifacts:
  files:
    - '**/*'
EOF

# Create appspec for CodeDeploy
cat > appspec.yml << EOF
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: root
EOF
```

#### Step 2: Create Build Project
```bash
aws codebuild create-project \
  --name my-web-app-build \
  --source type=CODECOMMIT,location=https://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-web-app \
  --artifacts type=CODEPIPELINE \
  --environment type=LINUX_CONTAINER,image=aws/codebuild/amazonlinux2-x86_64-standard:3.0,computeType=BUILD_GENERAL1_SMALL \
  --service-role arn:aws:iam::123456789012:role/CodeBuildRole
```

#### Step 3: Create Pipeline
```bash
aws codepipeline create-pipeline --cli-input-json file://pipeline-config.json
```

### Pipeline Configuration:
```json
{
  "pipeline": {
    "name": "MyWebAppPipeline",
    "roleArn": "arn:aws:iam::123456789012:role/CodePipelineRole",
    "artifactStore": {
      "type": "S3",
      "location": "my-pipeline-artifacts"
    },
    "stages": [
      {
        "name": "Source",
        "actions": [
          {
            "name": "Source",
            "actionTypeId": {
              "category": "Source",
              "owner": "AWS",
              "provider": "CodeCommit",
              "version": "1"
            },
            "configuration": {
              "RepositoryName": "my-web-app",
              "BranchName": "main"
            },
            "outputArtifacts": [{"name": "SourceOutput"}]
          }
        ]
      },
      {
        "name": "Build",
        "actions": [
          {
            "name": "Build",
            "actionTypeId": {
              "category": "Build",
              "owner": "AWS",
              "provider": "CodeBuild",
              "version": "1"
            },
            "configuration": {
              "ProjectName": "my-web-app-build"
            },
            "inputArtifacts": [{"name": "SourceOutput"}],
            "outputArtifacts": [{"name": "BuildOutput"}]
          }
        ]
      },
      {
        "name": "ApprovalForProduction",
        "actions": [
          {
            "name": "ManualApproval",
            "actionTypeId": {
              "category": "Approval",
              "owner": "AWS",
              "provider": "Manual",
              "version": "1"
            },
            "configuration": {
              "CustomData": "Please review and approve deployment to production"
            }
          }
        ]
      },
      {
        "name": "Deploy",
        "actions": [
          {
            "name": "Deploy",
            "actionTypeId": {
              "category": "Deploy",
              "owner": "AWS",
              "provider": "CodeDeploy",
              "version": "1"
            },
            "configuration": {
              "ApplicationName": "my-web-app",
              "DeploymentGroupName": "production"
            },
            "inputArtifacts": [{"name": "BuildOutput"}]
          }
        ]
      }
    ]
  }
}
```

## Hands-on Activities

### Activity 1: Basic Pipeline
1. Create a simple 3-stage pipeline (Source → Build → Deploy)
2. Test the pipeline with a code commit
3. Monitor execution in AWS Console

### Activity 2: Advanced Pipeline
1. Add parallel testing stages
2. Implement manual approval
3. Add deployment to multiple environments

### Activity 3: Pipeline Optimization
1. Implement caching in build stage
2. Add CloudWatch monitoring
3. Set up failure notifications

## Monitoring and Alerts

#### CloudWatch Metrics:
- Pipeline execution success/failure rates
- Stage duration metrics
- Build success rates

#### SNS Notifications:
```bash
aws codepipeline put-webhook \
  --webhook name=MyWebhook,targetPipeline=MyPipeline,targetAction=Source,filters=[{jsonPath="$.ref",matchEquals="refs/heads/main"}] \
  --authentication GITHUB_HMAC,SecretToken=mytoken
```

## Next Steps
Tomorrow we'll dive deeper into AWS CodeBuild to understand how to create sophisticated build processes and integrate testing frameworks.

## Additional Resources
- [CodePipeline User Guide](https://docs.aws.amazon.com/codepipeline/latest/userguide/)
- [Pipeline Structure Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
- [CodePipeline Best Practices](https://docs.aws.amazon.com/codepipeline/latest/userguide/best-practices.html)