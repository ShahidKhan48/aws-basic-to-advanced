# Auto Scaling Integrations - EC2, ALB & EKS

## 🖥️ EC2 Integration

### ASG and EC2 Instances
- **Instance Management**: ASG manages EC2 instance lifecycle
- **Health Monitoring**: Monitors instance health and replaces unhealthy instances
- **Distribution**: Distributes instances across AZs
- **Termination**: Handles instance termination based on policies

### Instance Types in ASG
```json
{
  "MixedInstancesPolicy": {
    "LaunchTemplate": {
      "LaunchTemplateSpecification": {
        "LaunchTemplateId": "lt-12345678",
        "Version": "$Latest"
      },
      "Overrides": [
        {
          "InstanceType": "t3.medium",
          "WeightedCapacity": "1"
        },
        {
          "InstanceType": "t3.large",
          "WeightedCapacity": "2"
        },
        {
          "InstanceType": "m5.large",
          "WeightedCapacity": "2"
        }
      ]
    }
  }
}
```

### Spot Instance Integration
- **Cost Savings**: Up to 90% cost reduction
- **Diversification**: Multiple instance types and AZs
- **Interruption Handling**: Graceful handling of Spot interruptions
- **Capacity Optimized**: AWS selects best Spot pools

### Spot Fleet Configuration
```json
{
  "InstancesDistribution": {
    "OnDemandBaseCapacity": 2,
    "OnDemandPercentageAboveBaseCapacity": 20,
    "SpotAllocationStrategy": "capacity-optimized",
    "SpotInstancePools": 4,
    "SpotMaxPrice": "0.10"
  }
}
```

## ⚖️ Application Load Balancer (ALB) Integration

### ASG with ALB
- **Target Registration**: ASG automatically registers instances with ALB
- **Health Checks**: ALB health checks determine instance health
- **Traffic Distribution**: ALB distributes traffic across healthy instances
- **Scaling Metrics**: Use ALB metrics for scaling decisions

### Target Group Integration
```json
{
  "AutoScalingGroupName": "web-asg",
  "TargetGroupARNs": [
    "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/web-targets/1234567890123456"
  ],
  "HealthCheckType": "ELB",
  "HealthCheckGracePeriod": 300
}
```

### ALB-Based Scaling Metrics
- **RequestCountPerTarget**: Scale based on requests per instance
- **TargetResponseTime**: Scale based on response time
- **HTTPCode_Target_2XX_Count**: Scale based on successful responses
- **HTTPCode_Target_5XX_Count**: Scale based on error responses

### ALB Target Tracking Example
```json
{
  "TargetTrackingConfiguration": {
    "TargetValue": 1000.0,
    "PredefinedMetricSpecification": {
      "PredefinedMetricType": "ALBRequestCountPerTarget",
      "ResourceLabel": "app/my-load-balancer/50dc6c495c0c9188/targetgroup/my-targets/73e2d6bc24d8a067"
    }
  }
}
```

### Connection Draining
- **Deregistration Delay**: Time for in-flight requests to complete
- **Graceful Shutdown**: Instances finish processing before termination
- **Configuration**: Set appropriate deregistration delay

```json
{
  "TargetGroupAttributes": [
    {
      "Key": "deregistration_delay.timeout_seconds",
      "Value": "300"
    }
  ]
}
```

## ☸️ EKS Integration

### EKS Cluster Autoscaler
- **Node Scaling**: Automatically scale EKS worker nodes
- **Pod Scheduling**: Scale when pods cannot be scheduled
- **Node Removal**: Remove underutilized nodes
- **Multi-AZ**: Scale across multiple AZs

### Cluster Autoscaler Configuration
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
spec:
  template:
    spec:
      containers:
      - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.21.0
        name: cluster-autoscaler
        command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/my-cluster
        - --balance-similar-node-groups
        - --skip-nodes-with-system-pods=false
```

### EKS Node Group ASG Tags
```json
{
  "Tags": [
    {
      "Key": "k8s.io/cluster-autoscaler/enabled",
      "Value": "true"
    },
    {
      "Key": "k8s.io/cluster-autoscaler/my-cluster",
      "Value": "owned"
    }
  ]
}
```

### EKS Managed Node Groups
- **AWS Managed**: AWS manages the ASG for you
- **Automatic Updates**: Rolling updates for node patches
- **Spot Support**: Built-in Spot instance support
- **Launch Templates**: Use launch templates for configuration

### EKS Managed Node Group Configuration
```json
{
  "NodegroupName": "managed-nodes",
  "ClusterName": "my-cluster",
  "Subnets": [
    "subnet-12345678",
    "subnet-87654321"
  ],
  "InstanceTypes": [
    "t3.medium",
    "t3.large"
  ],
  "ScalingConfig": {
    "MinSize": 1,
    "MaxSize": 10,
    "DesiredSize": 3
  },
  "CapacityType": "SPOT"
}
```

## 🔄 Multi-Service Integration

### ASG + ALB + EKS Architecture
```
Internet → ALB → EKS Ingress Controller → Kubernetes Services → Pods
                     ↓
               EKS Worker Nodes (ASG)
                     ↓
               Cluster Autoscaler
```

### Horizontal Pod Autoscaler (HPA)
- **Pod Scaling**: Scale pods based on metrics
- **Integration**: Works with Cluster Autoscaler
- **Metrics**: CPU, memory, custom metrics
- **Coordination**: HPA scales pods, Cluster Autoscaler scales nodes

### HPA Configuration
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-app
  minReplicas: 3
  maxReplicas: 100
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

## 📊 Monitoring Integrations

### CloudWatch Integration
- **ASG Metrics**: Instance counts, scaling activities
- **ALB Metrics**: Request count, response time, error rates
- **EKS Metrics**: Node and pod metrics
- **Custom Metrics**: Application-specific metrics

### CloudWatch Dashboard Example
```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/AutoScaling", "GroupDesiredCapacity", "AutoScalingGroupName", "web-asg"],
          [".", "GroupInServiceInstances", ".", "."],
          ["AWS/ApplicationELB", "RequestCount", "LoadBalancer", "app/my-alb/1234567890123456"]
        ],
        "period": 300,
        "stat": "Average",
        "region": "us-east-1",
        "title": "ASG and ALB Metrics"
      }
    }
  ]
}
```

### SNS Notifications
- **ASG Events**: Instance launch/terminate events
- **ALB Events**: Target health changes
- **EKS Events**: Node scaling events
- **Integration**: Trigger Lambda functions, send alerts

## 🔧 Advanced Integration Patterns

### Blue/Green Deployments with ASG
1. **Create New ASG**: With new application version
2. **Update ALB**: Gradually shift traffic to new ASG
3. **Monitor**: Watch metrics and health checks
4. **Complete**: Terminate old ASG when satisfied

### Canary Deployments
- **Weighted Routing**: Use ALB weighted target groups
- **Gradual Rollout**: Increase traffic percentage gradually
- **Monitoring**: Monitor metrics during rollout
- **Rollback**: Quick rollback if issues detected

### Multi-Region ASG
- **Cross-Region**: ASGs in multiple regions
- **Route 53**: Health check-based failover
- **Data Replication**: Ensure data consistency
- **Disaster Recovery**: Automatic failover capabilities

## 🎯 Best Practices

### EC2 Integration
- Use mixed instance types for cost optimization
- Implement proper health checks
- Configure appropriate termination policies
- Monitor Spot instance interruptions

### ALB Integration
- Use ELB health checks for web applications
- Configure appropriate deregistration delay
- Monitor target group health
- Use ALB metrics for scaling decisions

### EKS Integration
- Use Cluster Autoscaler for node scaling
- Implement HPA for pod scaling
- Monitor both node and pod metrics
- Use managed node groups when possible

### General Integration
- Monitor all components in the integration
- Implement proper alerting and notifications
- Test scaling scenarios regularly
- Document integration architecture and procedures