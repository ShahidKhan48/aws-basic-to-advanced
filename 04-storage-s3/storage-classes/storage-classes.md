# S3 Storage Classes

## 🏆 S3 Standard
- **Use Case**: Frequently accessed data
- **Durability**: 99.999999999% (11 9's)
- **Availability**: 99.99%
- **AZ Resilience**: ≥3 AZs
- **Retrieval**: Immediate
- **Cost**: Highest storage cost, no retrieval fees

## 📊 S3 Intelligent-Tiering (IA)
- **Use Case**: Data with unknown or changing access patterns
- **Features**:
  - Automatic cost optimization
  - Moves objects between access tiers
  - No retrieval fees
  - Monitoring and automation fees apply
- **Tiers**:
  - Frequent Access (same as Standard)
  - Infrequent Access (same as Standard-IA)
  - Archive Instant Access
  - Archive Access (optional)
  - Deep Archive Access (optional)

## 📉 S3 Standard-Infrequent Access (Standard-IA)
- **Use Case**: Infrequently accessed but requires rapid access
- **Durability**: 99.999999999% (11 9's)
- **Availability**: 99.9%
- **AZ Resilience**: ≥3 AZs
- **Retrieval**: Immediate
- **Cost**: Lower storage cost, retrieval fees apply
- **Minimum**: 30-day storage duration

## 🏝️ S3 One Zone-Infrequent Access (One Zone-IA)
- **Use Case**: Infrequently accessed, non-critical data
- **Durability**: 99.999999999% (11 9's) within single AZ
- **Availability**: 99.5%
- **AZ Resilience**: Single AZ (data lost if AZ destroyed)
- **Retrieval**: Immediate
- **Cost**: 20% less than Standard-IA
- **Minimum**: 30-day storage duration

## 🧊 S3 Glacier Instant Retrieval
- **Use Case**: Archive data requiring immediate access
- **Durability**: 99.999999999% (11 9's)
- **Availability**: 99.9%
- **AZ Resilience**: ≥3 AZs
- **Retrieval**: Immediate (milliseconds)
- **Cost**: Lower storage cost, higher retrieval cost
- **Minimum**: 90-day storage duration

## ❄️ S3 Glacier Flexible Retrieval
- **Use Case**: Archive data accessed 1-2 times per year
- **Durability**: 99.999999999% (11 9's)
- **Availability**: 99.99%
- **AZ Resilience**: ≥3 AZs
- **Retrieval Options**:
  - Expedited: 1-5 minutes
  - Standard: 3-5 hours
  - Bulk: 5-12 hours
- **Minimum**: 90-day storage duration

## 🏔️ S3 Glacier Deep Archive
- **Use Case**: Long-term archive, accessed less than once per year
- **Durability**: 99.999999999% (11 9's)
- **Availability**: 99.99%
- **AZ Resilience**: ≥3 AZs
- **Retrieval Options**:
  - Standard: 12 hours
  - Bulk: 48 hours
- **Cost**: Lowest storage cost
- **Minimum**: 180-day storage duration

## 📋 Storage Class Comparison

| Storage Class | Availability | Min Duration | Retrieval Time | Use Case |
|---------------|--------------|--------------|----------------|----------|
| Standard | 99.99% | None | Immediate | Frequent access |
| Intelligent-Tiering | 99.9% | None | Immediate | Unknown patterns |
| Standard-IA | 99.9% | 30 days | Immediate | Infrequent access |
| One Zone-IA | 99.5% | 30 days | Immediate | Non-critical IA |
| Glacier Instant | 99.9% | 90 days | Immediate | Archive with instant access |
| Glacier Flexible | 99.99% | 90 days | Minutes to hours | Archive |
| Glacier Deep Archive | 99.99% | 180 days | Hours | Long-term archive |

## 🎯 Choosing Storage Classes
- **Frequent Access**: Standard
- **Unknown Patterns**: Intelligent-Tiering
- **Backup/DR**: Standard-IA or One Zone-IA
- **Compliance Archive**: Glacier Flexible Retrieval
- **Long-term Backup**: Glacier Deep Archive