# EC2 Instance Types

## 🖥️ General Purpose (T, M Series)

### T Series - Burstable Performance
- **T4g, T3, T2**: CPU credits system
- **Use Cases**: Web servers, small databases, development
- **Features**:
  - Baseline CPU performance
  - Burst above baseline when needed
  - CPU credits accumulate when below baseline
- **Best For**: Variable workloads, cost optimization

### M Series - Balanced
- **M6i, M5, M4**: Balanced compute, memory, networking
- **Use Cases**: Web applications, microservices, enterprise apps
- **Features**:
  - Consistent performance
  - Good all-around choice
  - Various sizes available

## ⚡ Compute Optimized (C Series)
- **C6i, C5, C4**: High-performance processors
- **Features**:
  - High CPU-to-memory ratio
  - Enhanced networking
  - Latest generation processors
- **Use Cases**:
  - CPU-intensive applications
  - Scientific computing
  - Gaming servers
  - High-performance web servers

## 🧠 Memory Optimized (R, X Series)

### R Series - Memory Optimized
- **R6i, R5, R4**: High memory-to-CPU ratio
- **Use Cases**: In-memory databases, real-time analytics
- **Features**:
  - Large amounts of RAM
  - Good for memory-intensive applications

### X Series - Extreme Memory
- **X1e, X1**: Extreme memory capacity
- **Use Cases**: SAP HANA, Apache Spark, high-performance databases
- **Features**:
  - Highest memory-to-CPU ratio
  - Enterprise-grade performance

## 💾 Storage Optimized (I Series)
- **I4i, I3**: NVMe SSD storage
- **Features**:
  - High sequential read/write
  - Low latency storage
  - Local NVMe storage
- **Use Cases**:
  - Distributed file systems
  - Data warehousing
  - High-frequency online transaction processing

## 🎯 Choosing Instance Types
- **Development/Testing**: T3, T2 (burstable, cost-effective)
- **Web Applications**: M5, M4 (balanced performance)
- **CPU-Intensive**: C5, C4 (high compute)
- **Memory-Intensive**: R5, R4 (high memory)
- **Storage-Intensive**: I3 (high I/O performance)