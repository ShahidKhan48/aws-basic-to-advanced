# Auto Scaling Groups - Min, Max & Desired Capacity

## 🔄 Auto Scaling Group (ASG) Overview

### What is an ASG?
- **Definition**: Collection of EC2 instances managed as a logical group
- **Purpose**: Automatically adjust capacity based on demand
- **Benefits**: High availability, cost optimization, fault tolerance
- **Integration**: Works with ELB, CloudWatch, SNS

## 📊 Capacity Configuration

### Min, Max, Desired Capacity
- **Minimum Capacity**: Minimum number of instances (always running)
- **Maximum Capacity**: Maximum number of instances (scale limit)
- **Desired Capacity**: Target number of instances (current goal)

### Capacity Example
```
Min: 2 instances (always at least 2)
Desired: 4 instances (current target)
Max: 10 instances (never exceed 10)
```

### Capacity Rules
- **Min ≤ Desired ≤ Max**: Desired must be within min/max range
- **Automatic Adjustment**: ASG maintains desired capacity
- **Health Checks**: Replaces unhealthy instances
- **Scaling Policies**: Adjust desired capacity based on metrics

## 🚀 Launch Configuration vs Launch Template

### Launch Configuration (Legacy)
- **Definition**: Instance configuration for ASG
- **Immutable**: Cannot be modified after creation
- **Versioning**: No built-in versioning
- **Status**: Legacy, use Launch Templates instead

### Launch Template (Recommended)
- **Definition**: Modern instance configuration
- **Versioning**: Built-in version control
- **Flexibility**: Can be modified and versioned
- **Features**: More features than Launch Configuration

### Launch Template Configuration
```json
{
  "LaunchTemplateName": "web-server-template",
  "VersionDescription": "v1.0",
  "LaunchTemplateData": {
    "ImageId": "ami-12345678",
    "InstanceType": "t3.medium",
    "KeyName": "my-key-pair",
    "SecurityGroupIds": ["sg-12345678"],
    "UserData": "IyEvYmluL2Jhc2gKZWNobyAiSGVsbG8gV29ybGQi",
    "IamInstanceProfile": {
      "Arn": "arn:aws:iam::123456789012:instance-profile/MyRole"
    },
    "BlockDeviceMappings": [
      {
        "DeviceName": "/dev/xvda",
        "Ebs": {
          "VolumeSize": 20,
          "VolumeType": "gp3",
          "DeleteOnTermination": true
        }
      }
    ]
  }
}
```

## 🏗️ ASG Configuration

### Basic ASG Configuration
```json
{
  "AutoScalingGroupName": "web-asg",
  "LaunchTemplate": {
    "LaunchTemplateId": "lt-12345678",
    "Version": "$Latest"
  },
  "MinSize": 2,
  "MaxSize": 10,
  "DesiredCapacity": 4,
  "DefaultCooldown": 300,
  "HealthCheckType": "ELB",
  "HealthCheckGracePeriod": 300,
  "VPCZoneIdentifier": "subnet-12345678,subnet-87654321",
  "TargetGroupARNs": [
    "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/web-targets/1234567890123456"
  ]
}
```

### Availability Zones
- **Multi-AZ**: Distribute instances across multiple AZs
- **Balance**: ASG balances instances across AZs
- **Fault Tolerance**: Survive AZ failures
- **Best Practice**: Use at least 2 AZs

## 🏥 Health Checks

### Health Check Types
- **EC2 Health Check**: Instance status checks
- **ELB Health Check**: Load balancer health checks
- **Custom Health Check**: Application-specific checks

### EC2 Health Check
- **System Status**: AWS infrastructure health
- **Instance Status**: Instance software/network health
- **Replacement**: Terminates and replaces unhealthy instances

### ELB Health Check
- **Target Health**: Load balancer target health
- **More Accurate**: Application-level health
- **Recommended**: Use for web applications

### Health Check Configuration
```json
{
  "HealthCheckType": "ELB",
  "HealthCheckGracePeriod": 300
}
```

## ⏱️ Cooldown Period

### What is Cooldown?
- **Definition**: Time to wait before next scaling activity
- **Purpose**: Prevent rapid scaling (thrashing)
- **Default**: 300 seconds (5 minutes)
- **Scope**: Applies to simple scaling policies

### Cooldown Behavior
```
Scale Out Event → Cooldown Period → Can Scale Again
Instance Launch → Wait 300s → Next Scaling Decision
```

### Cooldown Best Practices
- **Appropriate Duration**: Match application startup time
- **Too Short**: Rapid scaling, wasted resources
- **Too Long**: Slow response to demand changes
- **Monitoring**: Adjust based on application behavior

## 🔄 Instance Lifecycle

### Lifecycle States
1. **Pending**: Instance launching
2. **InService**: Instance healthy and serving traffic
3. **Terminating**: Instance being terminated
4. **Terminated**: Instance terminated
5. **Detaching**: Instance being detached
6. **Detached**: Instance detached from ASG

### Lifecycle Hooks
- **Purpose**: Perform actions during instance lifecycle
- **Launch Hook**: Before instance enters InService
- **Terminate Hook**: Before instance terminates
- **Use Cases**: Configuration, cleanup, logging

### Lifecycle Hook Example
```json
{
  "LifecycleHookName": "instance-launch-hook",
  "AutoScalingGroupName": "web-asg",
  "LifecycleTransition": "autoscaling:EC2_INSTANCE_LAUNCHING",
  "DefaultResult": "CONTINUE",
  "HeartbeatTimeout": 300,
  "NotificationTargetARN": "arn:aws:sns:us-east-1:123456789012:asg-notifications"
}
```

## 🔧 Instance Protection

### What is Instance Protection?
- **Definition**: Prevent specific instances from scale-in termination
- **Use Cases**: Stateful instances, debugging, special workloads
- **Scope**: Per-instance setting
- **Override**: Can be manually terminated

### Enabling Instance Protection
```bash
aws autoscaling set-instance-protection \
    --instance-ids i-12345678 \
    --auto-scaling-group-name web-asg \
    --protected-from-scale-in
```

## 🎯 Termination Policies

### Default Termination Policy
1. Select AZ with most instances
2. Select instance with oldest launch configuration/template
3. Select instance closest to next billing hour

### Available Termination Policies
- **OldestInstance**: Terminate oldest instance
- **NewestInstance**: Terminate newest instance
- **OldestLaunchConfiguration**: Oldest launch config
- **OldestLaunchTemplate**: Oldest launch template
- **ClosestToNextInstanceHour**: Minimize costs
- **Default**: AWS default policy
- **AllocationStrategy**: For Spot instances

## 💰 Cost Optimization

### Spot Instances in ASG
- **Mixed Instance Policy**: Combine On-Demand and Spot
- **Cost Savings**: Up to 90% savings
- **Diversification**: Multiple instance types
- **Capacity Optimized**: AWS selects best Spot pools

### Mixed Instance Policy Example
```json
{
  "MixedInstancesPolicy": {
    "LaunchTemplate": {
      "LaunchTemplateSpecification": {
        "LaunchTemplateId": "lt-12345678",
        "Version": "$Latest"
      },
      "Overrides": [
        {"InstanceType": "t3.medium"},
        {"InstanceType": "t3a.medium"},
        {"InstanceType": "t2.medium"}
      ]
    },
    "InstancesDistribution": {
      "OnDemandBaseCapacity": 2,
      "OnDemandPercentageAboveBaseCapacity": 30,
      "SpotAllocationStrategy": "capacity-optimized"
    }
  }
}
```

## 📊 Monitoring

### CloudWatch Metrics
- **GroupMinSize**: Minimum group size
- **GroupMaxSize**: Maximum group size
- **GroupDesiredCapacity**: Desired capacity
- **GroupInServiceInstances**: Running instances
- **GroupPendingInstances**: Launching instances
- **GroupTerminatingInstances**: Terminating instances

### ASG Notifications
- **SNS Integration**: Send notifications to SNS topics
- **Events**: Launch, terminate, fail to launch, fail to terminate
- **Use Cases**: Monitoring, automation, alerting

## 🎯 Best Practices

### Design
- Use Launch Templates (not Launch Configurations)
- Deploy across multiple AZs
- Use ELB health checks for web applications
- Set appropriate min/max/desired capacity

### Performance
- Configure appropriate health check grace period
- Use lifecycle hooks for initialization
- Monitor scaling activities
- Optimize instance startup time

### Cost
- Use Spot instances for cost savings
- Implement appropriate termination policies
- Right-size instance types
- Monitor and optimize capacity

### Security
- Use IAM instance profiles
- Implement proper security groups
- Enable encryption for EBS volumes
- Regular security updates via new launch templates