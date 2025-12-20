# EKS - Control Plane, Worker Nodes, Node Groups & Auto Scaling

## ☸️ Amazon EKS Overview

### What is EKS?
- **Definition**: Managed Kubernetes service on AWS
- **Purpose**: Run Kubernetes without managing control plane
- **Benefits**: AWS integration, security, scalability, reliability
- **Compatibility**: Upstream Kubernetes, CNCF certified

## 🎛️ EKS Control Plane

### Control Plane Components
- **API Server**: Kubernetes API endpoint
- **etcd**: Distributed key-value store for cluster data
- **Controller Manager**: Manages controllers (deployments, services, etc.)
- **Scheduler**: Assigns pods to nodes
- **Cloud Controller Manager**: AWS-specific integrations

### Control Plane Features
- **Managed by AWS**: No maintenance required
- **High Availability**: Runs across multiple AZs
- **Automatic Updates**: Kubernetes version updates
- **Backup**: Automatic etcd backups
- **Monitoring**: Integrated with CloudWatch

### Control Plane Networking
- **VPC Integration**: Runs in AWS-managed VPC
- **ENI**: Elastic Network Interfaces in your VPC
- **Security Groups**: Control access to API server
- **Private Endpoint**: Optional private API access

## 🖥️ Worker Nodes (EC2)

### What are Worker Nodes?
- **Definition**: EC2 instances that run your containerized applications
- **Components**: kubelet, kube-proxy, container runtime
- **Management**: You manage the EC2 instances
- **Scaling**: Manual or automatic scaling

### Worker Node Components
- **kubelet**: Kubernetes node agent
- **kube-proxy**: Network proxy for services
- **Container Runtime**: Docker or containerd
- **AWS VPC CNI**: Networking plugin for pod networking
- **AWS Load Balancer Controller**: For load balancer integration

### Worker Node Types
- **On-Demand**: Standard EC2 pricing
- **Spot Instances**: Up to 90% cost savings
- **Reserved Instances**: Long-term cost savings
- **Mixed Instance Types**: Combine different instance types

## 👥 Node Groups

### What are Node Groups?
- **Definition**: Group of EC2 instances with same configuration
- **Management**: Simplified node lifecycle management
- **Scaling**: Automatic scaling capabilities
- **Updates**: Rolling updates for node patches

### Managed Node Groups
- **AWS Managed**: AWS handles node lifecycle
- **Features**:
  - Automatic scaling
  - Rolling updates
  - Spot instance support
  - Launch template integration
- **Benefits**: Simplified operations, best practices

### Self-Managed Node Groups
- **Customer Managed**: You handle node lifecycle
- **Flexibility**: Full control over node configuration
- **Responsibility**: Manual updates and scaling
- **Use Cases**: Custom requirements, specific configurations

### Node Group Configuration
```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: my-cluster
  region: us-west-2

nodeGroups:
  - name: worker-nodes
    instanceType: m5.large
    desiredCapacity: 3
    minSize: 1
    maxSize: 10
    volumeSize: 20
    ssh:
      allow: true
    iam:
      withAddonPolicies:
        autoScaler: true
        cloudWatch: true
```

## 📈 Auto Scaling

### Cluster Autoscaler
- **Purpose**: Automatically adjust number of nodes
- **Triggers**: Pod scheduling failures, node utilization
- **Scale Out**: Add nodes when pods can't be scheduled
- **Scale In**: Remove nodes when underutilized

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
```

### Horizontal Pod Autoscaler (HPA)
- **Purpose**: Scale pods based on metrics
- **Metrics**: CPU, memory, custom metrics
- **Integration**: Works with Cluster Autoscaler
- **Configuration**: HorizontalPodAutoscaler resource

### Vertical Pod Autoscaler (VPA)
- **Purpose**: Adjust pod resource requests/limits
- **Modes**: Off, Initial, Auto
- **Benefits**: Right-size pod resources
- **Limitation**: Cannot be used with HPA on same metric

## 🚀 EKS Fargate

### What is EKS Fargate?
- **Definition**: Serverless compute for containers
- **Benefits**: No node management, automatic scaling
- **Pricing**: Pay per pod vCPU and memory
- **Use Cases**: Batch jobs, CI/CD, microservices

### Fargate Profiles
- **Purpose**: Define which pods run on Fargate
- **Selectors**: Namespace and label selectors
- **Subnets**: Private subnets only
- **Execution Role**: IAM role for Fargate

```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: my-cluster
  region: us-west-2

fargateProfiles:
  - name: fp-default
    selectors:
      - namespace: default
      - namespace: kube-system
        labels:
          k8s-app: kube-dns
```

## 🔧 EKS Add-ons

### Core Add-ons
- **VPC CNI**: Pod networking
- **CoreDNS**: DNS resolution
- **kube-proxy**: Service networking
- **EBS CSI Driver**: Persistent volumes

### Additional Add-ons
- **AWS Load Balancer Controller**: ALB/NLB integration
- **Cluster Autoscaler**: Node scaling
- **Metrics Server**: Resource metrics
- **Fluent Bit**: Log forwarding

## 💰 Cost Optimization

### EKS Costs
- **Control Plane**: $0.10 per hour per cluster
- **Worker Nodes**: Standard EC2 pricing
- **Fargate**: Per pod vCPU and memory pricing
- **Data Transfer**: Standard AWS data transfer rates

### Cost Optimization Strategies
- Use Spot instances for non-critical workloads
- Right-size worker nodes and pods
- Use Fargate for variable workloads
- Implement cluster autoscaling
- Monitor and optimize resource usage

## 🎯 Best Practices

### Cluster Design
- Use private subnets for worker nodes
- Enable logging for audit and diagnostics
- Use multiple AZs for high availability
- Implement proper RBAC
- Use namespaces for workload isolation

### Security
- Enable cluster logging
- Use IAM roles for service accounts (IRSA)
- Implement network policies
- Scan container images for vulnerabilities
- Use private container registries

### Operations
- Monitor cluster and application metrics
- Implement proper backup strategies
- Use GitOps for application deployment
- Automate cluster updates and patches
- Implement disaster recovery procedures