# CIDR Block & Subnets

## 🌐 CIDR Block (Classless Inter-Domain Routing)

### What is CIDR?
- **Definition**: Method for allocating IP addresses and routing
- **Format**: IP address followed by slash and number (e.g., 10.0.0.0/16)
- **Subnet Mask**: Number after slash indicates network portion bits

### CIDR Examples
```
10.0.0.0/16    = 65,536 IP addresses (10.0.0.0 to 10.0.255.255)
10.0.0.0/24    = 256 IP addresses (10.0.0.0 to 10.0.0.255)
192.168.1.0/28 = 16 IP addresses (192.168.1.0 to 192.168.1.15)
```

### RFC 1918 Private IP Ranges
- **10.0.0.0/8**: 10.0.0.0 to 10.255.255.255 (16.7M addresses)
- **172.16.0.0/12**: 172.16.0.0 to 172.31.255.255 (1M addresses)
- **192.168.0.0/16**: 192.168.0.0 to 192.168.255.255 (65K addresses)

## 🏗️ VPC CIDR Planning
- **VPC Size**: Choose appropriate CIDR block size
- **Future Growth**: Plan for expansion
- **Avoid Overlap**: Don't overlap with on-premises networks
- **Multiple CIDRs**: VPC can have multiple CIDR blocks

### Common VPC CIDR Patterns
```
Production VPC:    10.0.0.0/16
Development VPC:   10.1.0.0/16
Testing VPC:       10.2.0.0/16
```

## 🏢 Subnets

### Public Subnets
- **Definition**: Subnet with route to Internet Gateway
- **Characteristics**:
  - Resources can have public IP addresses
  - Direct internet connectivity
  - Auto-assign public IP option available
- **Use Cases**:
  - Web servers
  - Load balancers
  - Bastion hosts
  - NAT gateways

### Private Subnets
- **Definition**: Subnet without direct route to Internet Gateway
- **Characteristics**:
  - Resources use private IP addresses only
  - Internet access via NAT Gateway/Instance
  - More secure for internal resources
- **Use Cases**:
  - Application servers
  - Databases
  - Internal services
  - Backend processing

## 📐 Subnet Design Patterns

### Multi-AZ Architecture
```
VPC: 10.0.0.0/16
├── Public Subnet AZ-A:  10.0.1.0/24
├── Public Subnet AZ-B:  10.0.2.0/24
├── Private Subnet AZ-A: 10.0.11.0/24
└── Private Subnet AZ-B: 10.0.12.0/24
```

### Three-Tier Architecture
```
VPC: 10.0.0.0/16
├── Web Tier (Public):    10.0.1.0/24, 10.0.2.0/24
├── App Tier (Private):   10.0.11.0/24, 10.0.12.0/24
└── DB Tier (Private):    10.0.21.0/24, 10.0.22.0/24
```

## 🎯 Best Practices
- Use /16 for VPC CIDR (provides flexibility)
- Use /24 for subnet CIDR (256 addresses)
- Reserve IP ranges for future use
- Distribute subnets across multiple AZs
- Use consistent naming conventions
- Document your IP allocation strategy