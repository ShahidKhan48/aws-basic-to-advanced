# Lambda - Event Driven, Stateless & Pay per Execution

## ⚡ AWS Lambda Overview

### What is Lambda?
- **Definition**: Serverless compute service that runs code in response to events
- **Purpose**: Execute code without managing servers
- **Benefits**: No server management, automatic scaling, pay-per-use pricing
- **Runtime**: Supports multiple programming languages

## 🎯 Event-Driven Architecture

### What is Event-Driven?
- **Definition**: Code executes in response to events/triggers
- **Reactive**: Functions respond to events automatically
- **Decoupled**: Loose coupling between components
- **Scalable**: Automatically scales with event volume

### Event Sources
- **AWS Services**: S3, DynamoDB, Kinesis, SQS, SNS, API Gateway
- **HTTP Requests**: API Gateway, ALB, Lambda Function URLs
- **Scheduled Events**: CloudWatch Events/EventBridge
- **Custom Events**: SDK, CLI, or other AWS services

### Event Source Examples
```json
{
  "S3 Event": {
    "eventSource": "aws:s3",
    "eventName": "ObjectCreated:Put",
    "s3": {
      "bucket": {"name": "my-bucket"},
      "object": {"key": "image.jpg"}
    }
  },
  "DynamoDB Event": {
    "eventSource": "aws:dynamodb",
    "eventName": "INSERT",
    "dynamodb": {
      "Keys": {"id": {"S": "123"}},
      "NewImage": {"name": {"S": "John"}}
    }
  }
}
```

## 🔄 Stateless Functions

### What is Stateless?
- **Definition**: Functions don't maintain state between invocations
- **Fresh Start**: Each invocation is independent
- **No Persistence**: No local storage between executions
- **External State**: Use external services for state (DynamoDB, S3, etc.)

### Stateless Design Patterns
```python
# ❌ Stateful (Don't do this)
counter = 0

def lambda_handler(event, context):
    global counter
    counter += 1  # State persists between invocations (unreliable)
    return counter

# ✅ Stateless (Correct approach)
import boto3

def lambda_handler(event, context):
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('counters')
    
    # Get current count from external storage
    response = table.get_item(Key={'id': 'main'})
    count = response.get('Item', {}).get('count', 0)
    
    # Increment and store
    table.put_item(Item={'id': 'main', 'count': count + 1})
    
    return count + 1
```

### Stateless Benefits
- **Reliability**: Consistent behavior across invocations
- **Scalability**: Easy to scale horizontally
- **Fault Tolerance**: No state loss on failures
- **Simplicity**: Easier to test and debug

## 💰 Pay-per-Execution Pricing

### Pricing Components
- **Requests**: $0.20 per 1 million requests
- **Duration**: Based on memory allocation and execution time
- **Memory**: 128 MB to 10,240 MB (10 GB)
- **Free Tier**: 1 million requests and 400,000 GB-seconds per month

### Duration Pricing (per GB-second)
```
Memory (MB)    Price per GB-second
128           $0.0000166667
512           $0.0000166667
1024          $0.0000166667
3008          $0.0000166667
```

### Cost Calculation Example
```
Function Configuration:
- Memory: 512 MB (0.5 GB)
- Average Duration: 200ms (0.2 seconds)
- Monthly Invocations: 1 million

Cost Calculation:
- Request Cost: 1M requests × $0.20/1M = $0.20
- Duration Cost: 1M × 0.5 GB × 0.2s × $0.0000166667 = $1.67
- Total Monthly Cost: $0.20 + $1.67 = $1.87
```

## 🛠️ Lambda Function Components

### Function Configuration
```json
{
  "FunctionName": "my-function",
  "Runtime": "python3.9",
  "Role": "arn:aws:iam::123456789012:role/lambda-execution-role",
  "Handler": "lambda_function.lambda_handler",
  "Code": {
    "ZipFile": "base64-encoded-zip"
  },
  "Description": "My Lambda function",
  "Timeout": 30,
  "MemorySize": 512,
  "Environment": {
    "Variables": {
      "ENV": "production",
      "DB_HOST": "db.example.com"
    }
  }
}
```

### Supported Runtimes
- **Python**: 3.7, 3.8, 3.9, 3.10, 3.11
- **Node.js**: 14.x, 16.x, 18.x
- **Java**: 8, 11, 17
- **C#**: .NET Core 3.1, .NET 6
- **Go**: 1.x
- **Ruby**: 2.7
- **Custom Runtime**: Bring your own runtime

### Handler Function
```python
# Python Handler
def lambda_handler(event, context):
    """
    event: Contains event data from trigger
    context: Runtime information and methods
    """
    print(f"Event: {event}")
    print(f"Context: {context}")
    
    # Process the event
    result = process_event(event)
    
    # Return response
    return {
        'statusCode': 200,
        'body': json.dumps(result)
    }
```

## 🔧 Lambda Features

### Environment Variables
- **Configuration**: Store configuration without hardcoding
- **Encryption**: Automatic encryption at rest
- **KMS**: Optional encryption with customer keys
- **Runtime Access**: Available as environment variables

### Layers
- **Purpose**: Share code and dependencies across functions
- **Benefits**: Reduce deployment package size, code reuse
- **Limit**: Up to 5 layers per function
- **Size**: 250 MB unzipped (all layers combined)

### Layer Example
```bash
# Create layer
aws lambda publish-layer-version \
    --layer-name my-dependencies \
    --description "Common dependencies" \
    --zip-file fileb://layer.zip \
    --compatible-runtimes python3.9

# Use layer in function
aws lambda update-function-configuration \
    --function-name my-function \
    --layers arn:aws:lambda:us-east-1:123456789012:layer:my-dependencies:1
```

### Concurrency Control
- **Reserved Concurrency**: Guarantee capacity for function
- **Provisioned Concurrency**: Pre-warm instances for low latency
- **Account Limit**: 1000 concurrent executions per region (default)

## 🚀 Lambda Deployment

### Deployment Package
- **Zip File**: Up to 50 MB (direct upload)
- **S3**: Up to 250 MB (unzipped)
- **Container Image**: Up to 10 GB
- **Dependencies**: Include all required libraries

### Container Image Deployment
```dockerfile
FROM public.ecr.aws/lambda/python:3.9

# Copy function code
COPY lambda_function.py ${LAMBDA_TASK_ROOT}

# Copy requirements
COPY requirements.txt .
RUN pip install -r requirements.txt --target "${LAMBDA_TASK_ROOT}"

# Set handler
CMD ["lambda_function.lambda_handler"]
```

### Versioning and Aliases
- **Versions**: Immutable snapshots of function
- **Aliases**: Mutable pointers to versions
- **Use Cases**: Blue/green deployments, canary releases

```bash
# Publish version
aws lambda publish-version --function-name my-function

# Create alias
aws lambda create-alias \
    --function-name my-function \
    --name PROD \
    --function-version 1

# Update alias (for deployments)
aws lambda update-alias \
    --function-name my-function \
    --name PROD \
    --function-version 2
```

## 🔍 Lambda Monitoring

### CloudWatch Integration
- **Logs**: Automatic log collection
- **Metrics**: Duration, errors, invocations, throttles
- **Alarms**: Set up alerts on metrics
- **Insights**: Query and analyze logs

### Key Metrics
- **Invocations**: Number of function invocations
- **Duration**: Execution time
- **Errors**: Number of failed invocations
- **Throttles**: Number of throttled invocations
- **ConcurrentExecutions**: Number of concurrent executions

### X-Ray Tracing
- **Distributed Tracing**: Track requests across services
- **Performance Analysis**: Identify bottlenecks
- **Error Analysis**: Debug errors and exceptions
- **Service Map**: Visualize service dependencies

## 🎯 Best Practices

### Function Design
- **Single Responsibility**: One function, one purpose
- **Stateless**: Don't rely on local state
- **Idempotent**: Same input produces same output
- **Error Handling**: Implement proper error handling

### Performance Optimization
- **Right-size Memory**: Balance performance and cost
- **Connection Reuse**: Reuse database connections
- **Minimize Cold Starts**: Use provisioned concurrency if needed
- **Optimize Dependencies**: Include only necessary libraries

### Security
- **Least Privilege**: Minimal IAM permissions
- **Environment Variables**: Encrypt sensitive data
- **VPC**: Use VPC for private resource access
- **Input Validation**: Validate all inputs

### Cost Optimization
- **Memory Allocation**: Right-size memory for workload
- **Execution Time**: Optimize code for faster execution
- **Reserved Concurrency**: Use for predictable workloads
- **Monitor Usage**: Regular cost analysis and optimization