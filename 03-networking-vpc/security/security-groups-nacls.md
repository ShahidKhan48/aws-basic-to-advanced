# Security Groups & NACLs

## 🛡️ Security Groups (Stateful)

### What are Security Groups?
- **Definition**: Virtual firewall controlling traffic at instance level
- **State**: Stateful (return traffic automatically allowed)
- **Default Behavior**: Deny all inbound, allow all outbound
- **Rules**: Allow rules only (no deny rules)

### Security Group Characteristics
- **Instance Level**: Applied to ENI (Elastic Network Interface)
- **Multiple SGs**: Instance can have multiple security groups
- **Rule Evaluation**: All rules evaluated (not first match)
- **Changes**: Take effect immediately

### Security Group Rules
```
Type: HTTP
Protocol: TCP
Port: 80
Source: 0.0.0.0/0 (anywhere)

Type: SSH
Protocol: TCP
Port: 22
Source: sg-12345678 (another security group)

Type: MySQL
Protocol: TCP
Port: 3306
Source: 10.0.0.0/16 (VPC CIDR)
```

## 🚧 Network ACLs (Stateless)

### What are NACLs?
- **Definition**: Network Access Control Lists - subnet-level firewall
- **State**: Stateless (must explicitly allow return traffic)
- **Default Behavior**: Default NACL allows all traffic
- **Rules**: Both allow and deny rules supported

### NACL Characteristics
- **Subnet Level**: Applied to entire subnet
- **Rule Numbers**: Rules processed in numerical order (lowest first)
- **First Match**: First matching rule applies
- **Stateless**: Must configure inbound and outbound rules separately

### NACL Rule Structure
```
Rule #  Type     Protocol  Port Range  Source/Dest    Allow/Deny
100     HTTP     TCP       80          0.0.0.0/0      ALLOW
200     HTTPS    TCP       443         0.0.0.0/0      ALLOW
300     SSH      TCP       22          10.0.0.0/16    ALLOW
*       ALL      ALL       ALL         0.0.0.0/0      DENY
```

## 🔄 Security Groups vs NACLs

| Feature | Security Groups | NACLs |
|---------|----------------|-------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow & Deny |
| Rule Processing | All rules | First match |
| Return Traffic | Automatic | Manual |
| Default | Deny inbound | Allow all |

## 🏗️ Security Architecture Patterns

### Web Tier Security
```
Security Group: web-sg
- HTTP (80) from 0.0.0.0/0
- HTTPS (443) from 0.0.0.0/0
- SSH (22) from bastion-sg
```

### Application Tier Security
```
Security Group: app-sg
- HTTP (8080) from web-sg
- SSH (22) from bastion-sg
```

### Database Tier Security
```
Security Group: db-sg
- MySQL (3306) from app-sg
- SSH (22) from bastion-sg
```

## 🎯 Best Practices

### Security Groups
- Use descriptive names and descriptions
- Reference other security groups instead of IP ranges
- Follow principle of least privilege
- Regular review and cleanup
- Separate security groups by function/tier

### NACLs
- Use as additional layer of security
- Keep rules simple and well-documented
- Leave room between rule numbers for future additions
- Test thoroughly due to stateless nature
- Use for subnet-level blocking of specific IPs