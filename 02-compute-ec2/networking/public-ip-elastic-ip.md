# EC2 Networking - Public IP & Elastic IP

## 🌐 Public IP Address
- **Definition**: IPv4 address reachable from internet
- **Assignment**: Automatically assigned to instances in public subnets
- **Characteristics**:
  - Changes when instance stops/starts
  - Released when instance terminates
  - Free of charge
  - Cannot be transferred between instances

### Public IP Behavior
- **Running Instance**: Retains same public IP
- **Stop/Start**: Gets new public IP address
- **Reboot**: Keeps same public IP address
- **Terminate**: Public IP is released

## 🎯 Elastic IP (EIP)
- **Definition**: Static IPv4 address designed for dynamic cloud computing
- **Characteristics**:
  - Persistent across instance stop/start
  - Can be reassigned to different instances
  - Associated with AWS account, not instance
  - Charged when not associated with running instance

### EIP Features
- **Allocation**: Reserve IP address in your account
- **Association**: Attach to EC2 instance or network interface
- **Reassignment**: Move between instances quickly
- **Release**: Return to AWS when no longer needed

### EIP Best Practices
- Use only when static IP is required
- Release unused EIPs to avoid charges
- Consider using load balancers instead for web applications
- Use Route 53 for DNS-based failover instead of EIP reassignment

## 🔧 Network Interfaces

### Elastic Network Interface (ENI)
- **Definition**: Virtual network card for EC2 instances
- **Features**:
  - Primary private IPv4 address
  - One or more secondary private IPv4 addresses
  - One Elastic IP per private IPv4 address
  - One public IPv4 address
  - One or more security groups
  - MAC address

### ENI Use Cases
- Management networks
- Network and security appliances
- Dual-homed instances with workloads on distinct subnets
- Low-budget, high-availability solutions

## 💰 Pricing Considerations
- **Public IP**: Free when instance is running
- **Elastic IP**: 
  - Free when associated with running instance
  - Charged when not associated or instance is stopped
  - Additional EIPs beyond first one are charged
- **Data Transfer**: Charges apply for data transfer to/from internet

## 🎯 Best Practices
- Use Elastic Load Balancers for web applications instead of EIPs
- Implement proper security groups for public instances
- Use private subnets for internal resources
- Consider IPv6 for future-proofing
- Monitor data transfer costs for public instances