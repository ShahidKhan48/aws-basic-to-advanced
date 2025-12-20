# Container Security - IRSA & IAM Integration

## 🔐 IRSA (IAM Roles for Service Accounts)

### What is IRSA?
- **Definition**: IAM Roles for Service Accounts
- **Purpose**: Provide AWS permissions to Kubernetes pods without storing credentials
- **Security**: Fine-grained permissions per service account
- **Integration**: Native integration between EKS and IAM

### How IRSA Works
1. **OIDC Provider**: EKS cluster has OIDC identity provider
2. **Service Account**: Kubernetes service account with annotations
3. **IAM Role**: IAM role trusts the OIDC provider
4. **Token Exchange**: Pod gets AWS credentials via STS
5. **AWS API Calls**: Pod uses temporary credentials

### IRSA Architecture
```
Pod → Service Account → OIDC Token → STS → Temporary AWS Credentials
```

## 🛠️ Setting Up IRSA

### Step 1: Create OIDC Identity Provider
```bash
# Get OIDC issuer URL
aws eks describe-cluster --name my-cluster --query "cluster.identity.oidc.issuer" --output text

# Create OIDC identity provider
eksctl utils associate-iam-oidc-provider --cluster my-cluster --approve
```

### Step 2: Create IAM Role with Trust Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::123456789012:oidc-provider/oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E:sub": "system:serviceaccount:default:my-service-account",
          "oidc.eks.us-east-1.amazonaws.com/id/EXAMPLED539D4633E53DE1B716D3041E:aud": "sts.amazonaws.com"
        }
      }
    }
  ]
}
```

### Step 3: Create Service Account
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/my-irsa-role
```

### Step 4: Use Service Account in Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: my-app:latest
    env:
    - name: AWS_REGION
      value: us-east-1
```

## 🔧 IAM Integration Patterns

### Application-Level Permissions
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-app-bucket/*"
    }
  ]
}
```

### Service-Level Permissions
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem"
      ],
      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/MyTable"
    }
  ]
}
```

### Cross-Service Permissions
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sqs:SendMessage",
        "sqs:ReceiveMessage",
        "sqs:DeleteMessage"
      ],
      "Resource": "arn:aws:sqs:us-east-1:123456789012:my-queue"
    },
    {
      "Effect": "Allow",
      "Action": [
        "sns:Publish"
      ],
      "Resource": "arn:aws:sns:us-east-1:123456789012:my-topic"
    }
  ]
}
```

## 🛡️ Container Security Best Practices

### Image Security
- **Base Images**: Use minimal, trusted base images
- **Vulnerability Scanning**: Enable ECR image scanning
- **Image Signing**: Use Docker Content Trust or Notary
- **Regular Updates**: Keep base images and dependencies updated

### Runtime Security
- **Non-Root Users**: Run containers as non-root users
- **Read-Only Filesystem**: Use read-only root filesystem
- **Resource Limits**: Set CPU and memory limits
- **Security Contexts**: Configure security contexts properly

### Network Security
- **Network Policies**: Implement Kubernetes network policies
- **Service Mesh**: Use Istio or AWS App Mesh for traffic encryption
- **Private Subnets**: Run worker nodes in private subnets
- **Security Groups**: Restrict network access

### Example Security Context
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: app
    image: my-app:latest
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
    resources:
      limits:
        cpu: 500m
        memory: 512Mi
      requests:
        cpu: 250m
        memory: 256Mi
```

## 🔍 Pod Security Standards

### Privileged
- **Level**: Most permissive
- **Use Case**: System-level workloads
- **Restrictions**: Minimal restrictions

### Baseline
- **Level**: Minimally restrictive
- **Use Case**: Common containerized workloads
- **Restrictions**: Prevents known privilege escalations

### Restricted
- **Level**: Heavily restricted
- **Use Case**: Security-critical applications
- **Restrictions**: Follows pod hardening best practices

### Pod Security Policy Example
```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim'
  runAsUser:
    rule: 'MustRunAsNonRoot'
  seLinux:
    rule: 'RunAsAny'
  fsGroup:
    rule: 'RunAsAny'
```

## 🔐 Secrets Management

### Kubernetes Secrets
- **Storage**: etcd encryption at rest
- **Access**: RBAC-controlled access
- **Mounting**: Mount as files or environment variables
- **Rotation**: Manual rotation required

### AWS Secrets Manager Integration
- **CSI Driver**: Secrets Store CSI Driver
- **Automatic Rotation**: Built-in rotation capabilities
- **Encryption**: KMS encryption
- **Audit**: CloudTrail logging

### External Secrets Operator
```yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: aws-secrets-manager
spec:
  provider:
    aws:
      service: SecretsManager
      region: us-east-1
      auth:
        jwt:
          serviceAccountRef:
            name: external-secrets-sa
---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: app-secret
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: aws-secrets-manager
    kind: SecretStore
  target:
    name: app-secret
    creationPolicy: Owner
  data:
  - secretKey: password
    remoteRef:
      key: prod/myapp/db
      property: password
```

## 📊 Monitoring and Auditing

### CloudTrail Integration
- **API Calls**: Log all EKS API calls
- **IRSA Usage**: Track role assumptions
- **Access Patterns**: Monitor unusual access

### CloudWatch Container Insights
- **Metrics**: CPU, memory, network, disk
- **Logs**: Application and system logs
- **Performance**: Container performance monitoring

### Falco for Runtime Security
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: falco
spec:
  template:
    spec:
      containers:
      - name: falco
        image: falcosecurity/falco:latest
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /host/var/run/docker.sock
          name: docker-socket
        - mountPath: /host/dev
          name: dev-fs
        - mountPath: /host/proc
          name: proc-fs
```

## 🎯 Security Best Practices

### IRSA Best Practices
- Use least privilege principle
- Create specific roles for each service
- Regular audit of role permissions
- Monitor role usage with CloudTrail
- Use conditions in trust policies

### General Container Security
- Regular security assessments
- Implement defense in depth
- Use admission controllers
- Enable audit logging
- Monitor runtime behavior
- Implement incident response procedures