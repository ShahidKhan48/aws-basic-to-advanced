# Key Pairs & Security Groups

## 🔐 Key Pairs

### What are Key Pairs?
- **Purpose**: Secure SSH access to Linux instances
- **Components**:
  - **Public Key**: Stored on EC2 instance
  - **Private Key**: Downloaded and stored securely by user
- **Encryption**: RSA 2048-bit encryption

### Key Pair Management
- **Creation**: AWS Console, CLI, or import existing
- **Storage**: Private key (.pem file) stored locally
- **Permissions**: chmod 400 for private key file
- **Usage**: ssh -i keypair.pem ec2-user@public-ip

### Best Practices
- Never share private keys
- Use different key pairs for different environments
- Store private keys securely
- Rotate keys regularly
- Use IAM roles when possible instead of keys

## 🛡️ Security Groups

### What are Security Groups?
- **Function**: Virtual firewall controlling traffic to/from instances
- **Level**: Instance level (not subnet level)
- **State**: Stateful (return traffic automatically allowed)
- **Default**: Deny all inbound, allow all outbound

### Security Group Rules
- **Allow Rules Only**: No deny rules (implicit deny)
- **Inbound Rules**: Control incoming traffic
- **Outbound Rules**: Control outgoing traffic
- **Sources/Destinations**:
  - IP addresses (CIDR blocks)
  - Other security groups
  - Prefix lists

### Common Configurations
```
SSH Access (Port 22):
- Source: Your IP address or bastion host SG
- Protocol: TCP
- Port: 22

HTTP/HTTPS (Ports 80/443):
- Source: 0.0.0.0/0 (anywhere)
- Protocol: TCP
- Ports: 80, 443

Database Access (Port 3306 for MySQL):
- Source: Application server security group
- Protocol: TCP
- Port: 3306
```

### Best Practices
- Follow principle of least privilege
- Use descriptive names and descriptions
- Reference other security groups instead of IP ranges
- Regular review and cleanup
- Separate security groups by function/tier
- Document security group purposes