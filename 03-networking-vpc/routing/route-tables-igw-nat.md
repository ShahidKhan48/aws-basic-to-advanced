# Route Tables, Internet Gateway & NAT Gateway

## 🗺️ Route Tables

### What are Route Tables?
- **Definition**: Set of rules (routes) that determine network traffic direction
- **Function**: Control traffic flow between subnets and to/from internet
- **Association**: Each subnet must be associated with a route table

### Route Table Components
- **Destination**: IP address range (CIDR block)
- **Target**: Where to send traffic (gateway, interface, etc.)
- **Priority**: Most specific route takes precedence

### Route Table Types
- **Main Route Table**: Default for VPC, handles local traffic
- **Custom Route Tables**: Created for specific routing requirements
- **Local Route**: Automatically created for VPC CIDR communication

### Example Routes
```
Destination     Target
10.0.0.0/16    Local (VPC internal traffic)
0.0.0.0/0      igw-12345 (Internet Gateway)
192.168.1.0/24 vgw-67890 (VPN Gateway)
```

## 🌐 Internet Gateway (IGW)

### What is Internet Gateway?
- **Definition**: Horizontally scaled, redundant, highly available VPC component
- **Purpose**: Allow communication between VPC and internet
- **Characteristics**:
  - One IGW per VPC
  - Highly available by design
  - No bandwidth constraints

### IGW Functions
- **Outbound**: Allows instances to reach internet
- **Inbound**: Allows internet to reach instances with public IPs
- **NAT**: Performs network address translation for public IPs

### IGW Requirements for Internet Access
1. Attach IGW to VPC
2. Add route to route table (0.0.0.0/0 → IGW)
3. Instance must have public IP or Elastic IP
4. Security groups must allow traffic
5. NACLs must allow traffic

## 🔄 NAT Gateway

### What is NAT Gateway?
- **Definition**: Managed service for outbound internet access from private subnets
- **Purpose**: Allow private subnet resources to reach internet without being reachable from internet
- **Managed**: Fully managed by AWS, highly available

### NAT Gateway Features
- **Bandwidth**: Up to 45 Gbps
- **Availability**: Redundant within AZ
- **Managed**: No maintenance required
- **Elastic IP**: Requires Elastic IP address

### NAT Gateway vs NAT Instance
| Feature | NAT Gateway | NAT Instance |
|---------|-------------|--------------|
| Management | Fully managed | Self-managed |
| Availability | Built-in redundancy | Manual setup |
| Bandwidth | Up to 45 Gbps | Depends on instance type |
| Cost | Higher | Lower (instance cost only) |
| Security Groups | Not supported | Supported |
| Bastion Host | Not supported | Can be configured |

## 🏗️ Routing Architecture Examples

### Public Subnet Route Table
```
Destination     Target
10.0.0.0/16    Local
0.0.0.0/0      igw-12345
```

### Private Subnet Route Table
```
Destination     Target
10.0.0.0/16    Local
0.0.0.0/0      nat-67890
```

## 🎯 Best Practices
- Create separate route tables for public and private subnets
- Use NAT Gateway for production workloads
- Deploy NAT Gateway in each AZ for high availability
- Monitor NAT Gateway data processing charges
- Use VPC endpoints to avoid NAT Gateway costs for AWS services
- Implement least privilege routing