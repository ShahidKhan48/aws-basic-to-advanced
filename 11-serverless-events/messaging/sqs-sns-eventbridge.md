# Messaging Services - SQS, SNS & EventBridge

## 📬 Amazon SQS (Simple Queue Service)

### What is SQS?
- **Definition**: Fully managed message queuing service
- **Purpose**: Decouple and scale microservices, distributed systems
- **Benefits**: Reliable, secure, scalable message delivery
- **Types**: Standard Queues and FIFO Queues

### Standard Queues
- **Throughput**: Nearly unlimited throughput
- **Delivery**: At-least-once delivery
- **Ordering**: Best-effort ordering
- **Use Cases**: High-throughput applications where order isn't critical

### FIFO Queues
- **Throughput**: Up to 300 messages/second (or 3000 with batching)
- **Delivery**: Exactly-once delivery
- **Ordering**: Strict message ordering
- **Use Cases**: Applications requiring exact order and no duplicates

### SQS Queue Configuration
```json
{
  "QueueName": "my-queue.fifo",
  "Attributes": {
    "FifoQueue": "true",
    "ContentBasedDeduplication": "true",
    "MessageRetentionPeriod": "1209600",
    "VisibilityTimeoutSeconds": "30",
    "ReceiveMessageWaitTimeSeconds": "20",
    "DelaySeconds": "0"
  }
}
```

### SQS Message Lifecycle
1. **Send**: Producer sends message to queue
2. **Receive**: Consumer receives message
3. **Process**: Consumer processes message
4. **Delete**: Consumer deletes message from queue

### Dead Letter Queues (DLQ)
- **Purpose**: Handle messages that can't be processed
- **Configuration**: Set maximum receive count
- **Benefits**: Prevent message loss, debug failed messages
- **Analysis**: Analyze failed messages for troubleshooting

```json
{
  "RedrivePolicy": {
    "deadLetterTargetArn": "arn:aws:sqs:us-east-1:123456789012:my-dlq",
    "maxReceiveCount": 3
  }
}
```

## 📢 Amazon SNS (Simple Notification Service)

### What is SNS?
- **Definition**: Fully managed pub/sub messaging service
- **Purpose**: Send messages to multiple subscribers simultaneously
- **Benefits**: Fan-out messaging, multiple protocols, reliable delivery
- **Pattern**: Publisher-Subscriber (Pub/Sub)

### SNS Components
- **Topics**: Communication channels for messages
- **Publishers**: Send messages to topics
- **Subscribers**: Receive messages from topics
- **Protocols**: HTTP/HTTPS, Email, SMS, SQS, Lambda, Mobile Push

### SNS Topic Configuration
```json
{
  "TopicName": "order-notifications",
  "Attributes": {
    "DisplayName": "Order Notifications",
    "DeliveryPolicy": {
      "http": {
        "defaultHealthyRetryPolicy": {
          "minDelayTarget": 20,
          "maxDelayTarget": 20,
          "numRetries": 3,
          "numMaxDelayRetries": 0,
          "backoffFunction": "linear"
        }
      }
    }
  }
}
```

### SNS Subscription Types
```bash
# Email subscription
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:123456789012:order-notifications \
    --protocol email \
    --notification-endpoint user@example.com

# SQS subscription
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:123456789012:order-notifications \
    --protocol sqs \
    --notification-endpoint arn:aws:sqs:us-east-1:123456789012:order-queue

# Lambda subscription
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:123456789012:order-notifications \
    --protocol lambda \
    --notification-endpoint arn:aws:lambda:us-east-1:123456789012:function:process-order
```

### Message Filtering
- **Purpose**: Deliver messages only to interested subscribers
- **Filter Policies**: JSON-based message filtering
- **Attributes**: Message attributes for filtering
- **Benefits**: Reduce unnecessary message delivery

```json
{
  "FilterPolicy": {
    "event_type": ["order_placed", "order_shipped"],
    "region": ["us-east-1", "us-west-2"],
    "amount": [{"numeric": [">=", 100]}]
  }
}
```

## 🌉 Amazon EventBridge

### What is EventBridge?
- **Definition**: Serverless event bus service
- **Purpose**: Connect applications using events from AWS services, SaaS, and custom apps
- **Benefits**: Event-driven architectures, loose coupling, scalable
- **Evolution**: Successor to CloudWatch Events

### EventBridge Components
- **Event Bus**: Receives and routes events
- **Rules**: Match events and route to targets
- **Targets**: Destinations for matched events
- **Events**: JSON messages describing state changes

### Event Bus Types
- **Default Bus**: Receives events from AWS services
- **Custom Bus**: For custom applications and third-party SaaS
- **Partner Bus**: For AWS partner integrations

### EventBridge Rule Configuration
```json
{
  "Name": "process-s3-uploads",
  "EventPattern": {
    "source": ["aws.s3"],
    "detail-type": ["Object Created"],
    "detail": {
      "bucket": {
        "name": ["my-upload-bucket"]
      },
      "object": {
        "key": [{"prefix": "images/"}]
      }
    }
  },
  "Targets": [
    {
      "Id": "1",
      "Arn": "arn:aws:lambda:us-east-1:123456789012:function:process-image",
      "RoleArn": "arn:aws:iam::123456789012:role/EventBridgeRole"
    }
  ]
}
```

### Event Pattern Examples
```json
{
  "EC2 State Change": {
    "source": ["aws.ec2"],
    "detail-type": ["EC2 Instance State-change Notification"],
    "detail": {
      "state": ["running", "stopped"]
    }
  },
  "Custom Application Event": {
    "source": ["myapp.orders"],
    "detail-type": ["Order Placed"],
    "detail": {
      "amount": [{"numeric": [">", 100]}],
      "region": ["us-east-1"]
    }
  }
}
```

## 🔄 Messaging Patterns

### Point-to-Point (SQS)
```
Producer → Queue → Consumer
```
- **Use Cases**: Task queues, work distribution
- **Benefits**: Load balancing, fault tolerance
- **Characteristics**: One message, one consumer

### Pub/Sub (SNS)
```
Publisher → Topic → Multiple Subscribers
```
- **Use Cases**: Notifications, event broadcasting
- **Benefits**: Fan-out, multiple protocols
- **Characteristics**: One message, multiple consumers

### Event-Driven (EventBridge)
```
Event Source → Event Bus → Rules → Targets
```
- **Use Cases**: Microservices integration, workflow automation
- **Benefits**: Loose coupling, event filtering
- **Characteristics**: Event routing based on content

## 🏗️ Integration Patterns

### SQS + Lambda Pattern
```python
# Lambda function triggered by SQS
def lambda_handler(event, context):
    for record in event['Records']:
        # Process SQS message
        message_body = record['body']
        process_message(message_body)
        
        # Message automatically deleted after successful processing
    
    return {'statusCode': 200}
```

### SNS + SQS Fan-out Pattern
```
Order Service → SNS Topic → [Inventory SQS, Shipping SQS, Email SQS]
```

### EventBridge + Multiple Targets
```json
{
  "Targets": [
    {
      "Id": "lambda-target",
      "Arn": "arn:aws:lambda:us-east-1:123456789012:function:process-event"
    },
    {
      "Id": "sqs-target",
      "Arn": "arn:aws:sqs:us-east-1:123456789012:event-queue"
    },
    {
      "Id": "sns-target",
      "Arn": "arn:aws:sns:us-east-1:123456789012:notifications"
    }
  ]
}
```

## 🔒 Security and Access Control

### IAM Policies for SQS
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
    }
  ]
}
```

### SNS Topic Policies
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/PublisherRole"
      },
      "Action": "sns:Publish",
      "Resource": "arn:aws:sns:us-east-1:123456789012:my-topic"
    }
  ]
}
```

### EventBridge Resource Policies
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:root"
      },
      "Action": "events:PutEvents",
      "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/custom-bus"
    }
  ]
}
```

## 💰 Pricing Comparison

### SQS Pricing
- **Standard Queue**: $0.40 per million requests
- **FIFO Queue**: $0.50 per million requests
- **Data Transfer**: Standard AWS data transfer rates

### SNS Pricing
- **Requests**: $0.50 per million requests
- **Notifications**: Varies by protocol (Email: $2.00 per 100,000)
- **Data Transfer**: Standard AWS data transfer rates

### EventBridge Pricing
- **Custom Events**: $1.00 per million events
- **AWS Service Events**: Free
- **Cross-Region Replication**: $0.01 per GB

## 🎯 Best Practices

### SQS Best Practices
- Use FIFO queues only when ordering is critical
- Implement dead letter queues for error handling
- Set appropriate visibility timeout
- Use long polling to reduce costs
- Batch messages when possible

### SNS Best Practices
- Use message filtering to reduce unnecessary deliveries
- Implement retry policies for reliable delivery
- Use appropriate delivery protocols
- Monitor failed deliveries
- Secure topics with proper IAM policies

### EventBridge Best Practices
- Use descriptive event patterns
- Implement proper error handling
- Use custom event buses for organization
- Monitor rule executions
- Design events for forward compatibility

### General Messaging Best Practices
- Design for idempotency
- Implement proper error handling and retries
- Monitor message processing metrics
- Use appropriate message sizes
- Secure message content when necessary