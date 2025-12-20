# Scaling Policies - Target Tracking, Step Scaling & Scheduled

## 📈 Scaling Policies Overview

### What are Scaling Policies?
- **Definition**: Rules that define when and how to scale ASG capacity
- **Types**: Target Tracking, Step Scaling, Simple Scaling, Scheduled Scaling
- **Triggers**: CloudWatch metrics, time-based schedules
- **Actions**: Scale out (add instances) or scale in (remove instances)

## 🎯 Target Tracking Scaling

### What is Target Tracking?
- **Definition**: Maintain a specific metric value (target)
- **Automatic**: AWS automatically calculates scaling actions
- **Simplicity**: Easiest scaling policy to configure
- **Recommended**: Best practice for most use cases

### Target Tracking Configuration
```json
{
  "PolicyName": "cpu-target-tracking",
  "PolicyType": "TargetTrackingScaling",
  "TargetTrackingConfiguration": {
    "TargetValue": 70.0,
    "PredefinedMetricSpecification": {
      "PredefinedMetricType": "ASGAverageCPUUtilization"
    },
    "ScaleOutCooldown": 300,
    "ScaleInCooldown": 300,
    "DisableScaleIn": false
  }
}
```

### Predefined Metrics
- **ASGAverageCPUUtilization**: Average CPU across instances
- **ASGAverageNetworkIn**: Average network input
- **ASGAverageNetworkOut**: Average network output
- **ALBRequestCountPerTarget**: Requests per target (ALB)

### Custom Metrics Example
```json
{
  "TargetTrackingConfiguration": {
    "TargetValue": 1000.0,
    "CustomMetricSpecification": {
      "MetricName": "ActiveConnections",
      "Namespace": "MyApp/LoadBalancer",
      "Statistic": "Average",
      "Dimensions": [
        {
          "Name": "LoadBalancer",
          "Value": "my-load-balancer"
        }
      ]
    }
  }
}
```

### Target Tracking Benefits
- **Automatic Calculation**: AWS handles scaling math
- **Predictive**: Anticipates future needs
- **Stable**: Reduces scaling oscillations
- **Multiple Policies**: Can use multiple target tracking policies

## 📊 Step Scaling

### What is Step Scaling?
- **Definition**: Scale based on metric value ranges (steps)
- **Flexibility**: Different scaling amounts for different metric ranges
- **Precision**: Fine-grained control over scaling behavior
- **Complexity**: More complex than target tracking

### Step Scaling Configuration
```json
{
  "PolicyName": "cpu-step-scaling-out",
  "PolicyType": "StepScaling",
  "StepScalingPolicyConfiguration": {
    "AdjustmentType": "ChangeInCapacity",
    "Cooldown": 300,
    "MetricAggregationType": "Average",
    "StepAdjustments": [
      {
        "MetricIntervalLowerBound": 0,
        "MetricIntervalUpperBound": 20,
        "ScalingAdjustment": 1
      },
      {
        "MetricIntervalLowerBound": 20,
        "MetricIntervalUpperBound": 40,
        "ScalingAdjustment": 2
      },
      {
        "MetricIntervalLowerBound": 40,
        "ScalingAdjustment": 3
      }
    ]
  }
}
```

### Step Scaling Example
```
CPU Utilization Thresholds:
70-90%: Add 1 instance
90-95%: Add 2 instances
>95%:   Add 3 instances

Scale-In Thresholds:
<50%:   Remove 1 instance
<30%:   Remove 2 instances
```

### Adjustment Types
- **ChangeInCapacity**: Add/remove specific number of instances
- **ExactCapacity**: Set exact number of instances
- **PercentChangeInCapacity**: Change by percentage

### CloudWatch Alarm for Step Scaling
```json
{
  "AlarmName": "HighCPUUtilization",
  "MetricName": "CPUUtilization",
  "Namespace": "AWS/EC2",
  "Statistic": "Average",
  "Period": 300,
  "EvaluationPeriods": 2,
  "Threshold": 70,
  "ComparisonOperator": "GreaterThanThreshold",
  "AlarmActions": [
    "arn:aws:autoscaling:us-east-1:123456789012:scalingPolicy:policy-id"
  ]
}
```

## ⏰ Scheduled Scaling

### What is Scheduled Scaling?
- **Definition**: Scale based on time schedules
- **Predictable**: For predictable traffic patterns
- **Proactive**: Scale before demand increases
- **Use Cases**: Business hours, seasonal patterns, batch jobs

### Scheduled Scaling Configuration
```json
{
  "ScheduledActionName": "scale-up-business-hours",
  "Schedule": "0 8 * * MON-FRI",
  "MinSize": 5,
  "MaxSize": 20,
  "DesiredCapacity": 10,
  "TimeZone": "America/New_York"
}
```

### Schedule Formats
- **Cron Expression**: `0 8 * * MON-FRI` (8 AM weekdays)
- **At Expression**: `at(2024-01-15T08:00:00)` (specific date/time)
- **Rate Expression**: `rate(1 hour)` (every hour)

### Scheduled Scaling Examples
```bash
# Scale up for business hours (8 AM - 6 PM weekdays)
aws autoscaling put-scheduled-update-group-action \
    --auto-scaling-group-name web-asg \
    --scheduled-action-name scale-up-business \
    --recurrence "0 8 * * MON-FRI" \
    --desired-capacity 10

# Scale down after business hours
aws autoscaling put-scheduled-update-group-action \
    --auto-scaling-group-name web-asg \
    --scheduled-action-name scale-down-evening \
    --recurrence "0 18 * * MON-FRI" \
    --desired-capacity 3
```

### Scheduled Scaling Use Cases
- **Business Hours**: Scale up during work hours
- **Batch Processing**: Scale for nightly batch jobs
- **Seasonal Traffic**: Prepare for known traffic spikes
- **Cost Optimization**: Scale down during low usage periods

## 🔄 Simple Scaling (Legacy)

### What is Simple Scaling?
- **Definition**: Basic scaling based on single alarm
- **Legacy**: Older scaling method
- **Limitation**: Cooldown period applies to all scaling
- **Recommendation**: Use Step Scaling instead

### Simple Scaling Configuration
```json
{
  "PolicyName": "simple-scale-up",
  "PolicyType": "SimpleScaling",
  "AdjustmentType": "ChangeInCapacity",
  "ScalingAdjustment": 2,
  "Cooldown": 300
}
```

## 🔧 Scaling Policy Best Practices

### Policy Selection Guidelines
- **Target Tracking**: Default choice for most applications
- **Step Scaling**: When you need fine-grained control
- **Scheduled Scaling**: For predictable patterns
- **Combination**: Use multiple policies together

### Multiple Policies
```
Application Scaling Strategy:
├── Target Tracking: CPU 70% (primary)
├── Step Scaling: Memory-based (secondary)
└── Scheduled: Business hours (proactive)
```

### Cooldown Configuration
- **Scale-Out Cooldown**: Time after scale-out before next scale-out
- **Scale-In Cooldown**: Time after scale-in before next scale-in
- **Recommendation**: Scale-in cooldown > Scale-out cooldown

### Metric Selection
- **CPU Utilization**: Good for compute-intensive applications
- **Request Count**: Good for web applications
- **Custom Metrics**: Application-specific metrics
- **Multiple Metrics**: Use different metrics for different scenarios

## 📊 Monitoring Scaling Activities

### CloudWatch Metrics
- **GroupTotalInstances**: Total instances in ASG
- **GroupInServiceInstances**: Healthy instances
- **GroupPendingInstances**: Launching instances
- **GroupTerminatingInstances**: Terminating instances

### Scaling Activity History
```bash
# View scaling activities
aws autoscaling describe-scaling-activities \
    --auto-scaling-group-name web-asg \
    --max-items 10
```

### Scaling Notifications
```json
{
  "NotificationConfiguration": {
    "AutoScalingGroupName": "web-asg",
    "NotificationTypes": [
      "autoscaling:EC2_INSTANCE_LAUNCH",
      "autoscaling:EC2_INSTANCE_TERMINATE",
      "autoscaling:EC2_INSTANCE_LAUNCH_ERROR",
      "autoscaling:EC2_INSTANCE_TERMINATE_ERROR"
    ],
    "TopicARN": "arn:aws:sns:us-east-1:123456789012:asg-notifications"
  }
}
```

## 🎯 Optimization Strategies

### Prevent Thrashing
- **Appropriate Cooldowns**: Prevent rapid scaling
- **Stable Metrics**: Use stable, representative metrics
- **Gradual Scaling**: Avoid large scaling adjustments
- **Testing**: Test scaling policies under load

### Cost Optimization
- **Scale-In Policies**: Ensure instances scale in when not needed
- **Appropriate Thresholds**: Don't over-provision
- **Scheduled Scaling**: Proactively scale for known patterns
- **Spot Instances**: Use Spot instances for cost savings

### Performance Optimization
- **Predictive Scaling**: Use scheduled scaling for known patterns
- **Multiple Metrics**: Use different metrics for different scenarios
- **Fast Response**: Use appropriate evaluation periods
- **Health Checks**: Ensure new instances are healthy before serving traffic

## 🚨 Troubleshooting

### Common Issues
- **Scaling Not Triggering**: Check CloudWatch alarms and thresholds
- **Instances Not Launching**: Check launch template and capacity limits
- **Rapid Scaling**: Adjust cooldown periods and thresholds
- **Cost Issues**: Review scaling policies and instance usage

### Debugging Steps
1. **Check Scaling History**: Review scaling activities
2. **Verify Metrics**: Ensure metrics are being collected
3. **Test Alarms**: Manually trigger CloudWatch alarms
4. **Review Logs**: Check ASG and instance logs
5. **Validate Configuration**: Verify policy configuration