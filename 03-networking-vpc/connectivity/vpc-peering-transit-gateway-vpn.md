# VPC Peering, Transit Gateway & VPN

## 🔗 VPC Peering

### What is VPC Peering?
- **Definition**: Network connection between two VPCs
- **Purpose**: Route traffic privately using private IP addresses
- **Scope**: Same region or cross-region, same account or cross-account

### VPC Peering Characteristics
- **One-to-One**: Direct connection between two VPCs
- **Non-Transitive**: A-B and B-C doesn't mean A-C connection
- **No Overlapping CIDRs**: VPCs must have non-overlapping IP ranges
- **Route Tables**: Must update route tables in both VPCs

### VPC Peering Limitations
- No transitive peering
- No edge-to-edge routing
- Maximum 125 peering connections per VPC
- Cannot peer with VPCs that have matching CIDR blocks

## 🌐 Transit Gateway

### What is Transit Gateway?
- **Definition**: Central hub for connecting VPCs and on-premises networks
- **Purpose**: Simplify network architecture and reduce complexity
- **Scalability**: Supports thousands of VPCs and VPN connections

### Transit Gateway Features
- **Hub-and-Spoke**: Central connection point
- **Transitive Routing**: Full mesh connectivity
- **Cross-Region**: Connect across AWS regions
- **Route Tables**: Control traffic flow between attachments
- **Bandwidth**: Up to 50 Gbps per attachment

### Transit Gateway Components
- **Attachments**: VPCs, VPN connections, Direct Connect gateways
- **Route Tables**: Control routing between attachments
- **Route Propagation**: Automatic route learning
- **Associations**: Link attachments to route tables

## 🔐 VPN Connections

### Site-to-Site VPN
- **Definition**: Secure connection between on-premises and AWS
- **Components**:
  - Virtual Private Gateway (VGW) or Transit Gateway
  - Customer Gateway (CGW)
  - VPN Connection
- **Encryption**: IPSec encryption
- **Redundancy**: Two tunnels for high availability

### Client VPN
- **Definition**: Managed client-based VPN service
- **Purpose**: Secure access for remote users
- **Features**:
  - OpenVPN-based
  - Certificate-based authentication
  - Split tunneling support

## 🏗️ Connectivity Patterns

### Hub-and-Spoke with Transit Gateway
```
On-Premises ← VPN → Transit Gateway ← VPC Attachments
                         ↓
                    Route Tables
                         ↓
                   VPC-A, VPC-B, VPC-C
```

### Multi-Region Connectivity
```
Region A: VPCs → Transit Gateway → Inter-Region Peering
                                        ↓
Region B: Transit Gateway ← VPCs
```

## 💰 Cost Considerations

### VPC Peering
- **Data Transfer**: Charged for cross-AZ and cross-region traffic
- **Connection**: No hourly charges for peering connection itself

### Transit Gateway
- **Hourly Charges**: Per attachment and per Transit Gateway
- **Data Processing**: Charged per GB processed
- **Cross-Region**: Additional charges for inter-region peering

### VPN
- **Connection Hours**: Charged per VPN connection hour
- **Data Transfer**: Standard data transfer rates apply

## 🎯 Best Practices
- Use Transit Gateway for complex multi-VPC architectures
- Use VPC Peering for simple two-VPC connections
- Plan IP addressing to avoid CIDR conflicts
- Implement proper route table management
- Monitor costs for data transfer and processing
- Use Direct Connect for high-bandwidth, consistent connectivity