# CloudWatch Monitoring - Logs, Metrics & Alarms

## 📊 CloudWatch Overview

### What is CloudWatch?
- **Definition**: Monitoring and observability service for AWS resources and applications
- **Purpose**: Collect, monitor, and analyze metrics, logs, and events
- **Benefits**: Real-time monitoring, automated responses, operational insights
- **Integration**: Native integration with all AWS services

## 📝 CloudWatch Logs

### What are CloudWatch Logs?
- **Definition**: Centralized log management service
- **Purpose**: Monitor, store, and access log files from AWS resources
- **Benefits**: Real-time monitoring, log retention, search and filter
- **Integration**: Lambda, EC2, ECS, API Gateway, and more

### Log Groups and Log Streams
- **Log Group**: Collection of log streams with same retention and access control
- **Log Stream**: Sequence of log events from same source
- **Log Events**: Individual log entries with timestamp and message

### Log Group Configuration
```json
{
  "LogGroupName": "/aws/lambda/my-function",
  "RetentionInDays": 14,
  "KmsKeyId": "arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012"
}
```

### Lambda Logging Example
```python
import json
import logging

# Configure logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    # Log event details
    logger.info(f"Received event: {json.dumps(event)}")
    
    try:
        # Process event
        result = process_event(event)
        logger.info(f"Processing successful: {result}")
        
        return {
            'statusCode': 200,
            'body': json.dumps(result)
        }
    except Exception as e:
        logger.error(f"Processing failed: {str(e)}")
        raise
```

### Log Insights Queries
```sql
-- Find errors in Lambda logs
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 100

-- Analyze API Gateway response times
fields @timestamp, @duration
| filter @type = "REPORT"
| stats avg(@duration), max(@duration), min(@duration) by bin(5m)

-- Count log events by level
fields @timestamp, @message
| filter @message like /INFO|ERROR|WARN/
| stats count() by @message
```

## 📈 CloudWatch Metrics

### What are CloudWatch Metrics?
- **Definition**: Time-ordered set of data points
- **Purpose**: Monitor performance and health of AWS resources
- **Types**: Standard metrics (AWS services) and custom metrics
- **Dimensions**: Name-value pairs that identify metrics

### Standard Metrics Examples
```
EC2 Metrics:
- CPUUtilization
- NetworkIn/NetworkOut
- DiskReadOps/DiskWriteOps

Lambda Metrics:
- Invocations
- Duration
- Errors
- Throttles

ALB Metrics:
- RequestCount
- TargetResponseTime
- HTTPCode_Target_2XX_Count
```

### Custom Metrics
```python
import boto3

cloudwatch = boto3.client('cloudwatch')

# Put custom metric
cloudwatch.put_metric_data(
    Namespace='MyApp/Orders',
    MetricData=[
        {
            'MetricName': 'OrdersProcessed',
            'Value': 1,
            'Unit': 'Count',
            'Dimensions': [
                {
                    'Name': 'Environment',
                    'Value': 'Production'
                },
                {
                    'Name': 'Region',
                    'Value': 'us-east-1'
                }
            ]
        }
    ]
)
```

### Metric Math
```json
{
  "MetricDataQueries": [
    {
      "Id": "error_rate",
      "Expression": "errors / invocations * 100",
      "Label": "Error Rate (%)"
    },
    {
      "Id": "errors",
      "MetricStat": {
        "Metric": {
          "Namespace": "AWS/Lambda",
          "MetricName": "Errors",
          "Dimensions": [
            {
              "Name": "FunctionName",
              "Value": "my-function"
            }
          ]
        },
        "Period": 300,
        "Stat": "Sum"
      }
    },
    {
      "Id": "invocations",
      "MetricStat": {
        "Metric": {
          "Namespace": "AWS/Lambda",
          "MetricName": "Invocations",
          "Dimensions": [
            {
              "Name": "FunctionName",
              "Value": "my-function"
            }
          ]
        },
        "Period": 300,
        "Stat": "Sum"
      }
    }
  ]
}
```

## 🚨 CloudWatch Alarms

### What are CloudWatch Alarms?
- **Definition**: Watch metrics and trigger actions when thresholds are breached
- **Purpose**: Automated monitoring and response to metric changes
- **States**: OK, ALARM, INSUFFICIENT_DATA
- **Actions**: SNS notifications, Auto Scaling, EC2 actions

### Alarm Configuration
```json
{
  "AlarmName": "HighCPUUtilization",
  "AlarmDescription": "Alarm when CPU exceeds 80%",
  "MetricName": "CPUUtilization",
  "Namespace": "AWS/EC2",
  "Statistic": "Average",
  "Period": 300,
  "EvaluationPeriods": 2,
  "Threshold": 80,
  "ComparisonOperator": "GreaterThanThreshold",
  "Dimensions": [
    {
      "Name": "InstanceId",
      "Value": "i-12345678"
    }
  ],
  "AlarmActions": [
    "arn:aws:sns:us-east-1:123456789012:high-cpu-alert"
  ],
  "OKActions": [
    "arn:aws:sns:us-east-1:123456789012:cpu-normal"
  ]
}
```

### Composite Alarms
```json
{
  "AlarmName": "ApplicationHealthAlarm",
  "AlarmRule": "(ALARM(HighErrorRate) OR ALARM(HighLatency)) AND ALARM(LowThroughput)",
  "AlarmDescription": "Composite alarm for application health",
  "AlarmActions": [
    "arn:aws:sns:us-east-1:123456789012:critical-alerts"
  ]
}
```

### Alarm Actions
- **SNS Notifications**: Send alerts to SNS topics
- **Auto Scaling Actions**: Trigger scaling policies
- **EC2 Actions**: Stop, terminate, reboot, or recover instances
- **Systems Manager Actions**: Execute automation documents

## 📊 CloudWatch Dashboards

### What are Dashboards?
- **Definition**: Customizable home pages for monitoring resources
- **Purpose**: Visualize metrics and logs in one place
- **Widgets**: Graphs, numbers, text, logs
- **Sharing**: Share dashboards across accounts

### Dashboard Configuration
```json
{
  "DashboardName": "ApplicationMonitoring",
  "DashboardBody": {
    "widgets": [
      {
        "type": "metric",
        "properties": {
          "metrics": [
            ["AWS/Lambda", "Invocations", "FunctionName", "my-function"],
            [".", "Errors", ".", "."],
            [".", "Duration", ".", "."]
          ],
          "period": 300,
          "stat": "Average",
          "region": "us-east-1",
          "title": "Lambda Metrics"
        }
      },
      {
        "type": "log",
        "properties": {
          "query": "SOURCE '/aws/lambda/my-function'\n| fields @timestamp, @message\n| filter @message like /ERROR/\n| sort @timestamp desc\n| limit 100",
          "region": "us-east-1",
          "title": "Recent Errors"
        }
      }
    ]
  }
}
```

## 🔍 Application Monitoring Patterns

### Three Pillars of Observability
1. **Metrics**: Quantitative measurements over time
2. **Logs**: Detailed event records
3. **Traces**: Request flow through distributed systems

### Lambda Monitoring Pattern
```python
import json
import time
import boto3
from datetime import datetime

cloudwatch = boto3.client('cloudwatch')

def lambda_handler(event, context):
    start_time = time.time()
    
    try:
        # Process event
        result = process_event(event)
        
        # Custom success metric
        cloudwatch.put_metric_data(
            Namespace='MyApp/Lambda',
            MetricData=[
                {
                    'MetricName': 'ProcessingSuccess',
                    'Value': 1,
                    'Unit': 'Count',
                    'Timestamp': datetime.utcnow()
                }
            ]
        )
        
        return {
            'statusCode': 200,
            'body': json.dumps(result)
        }
        
    except Exception as e:
        # Custom error metric
        cloudwatch.put_metric_data(
            Namespace='MyApp/Lambda',
            MetricData=[
                {
                    'MetricName': 'ProcessingError',
                    'Value': 1,
                    'Unit': 'Count',
                    'Timestamp': datetime.utcnow()
                }
            ]
        )
        raise
    
    finally:
        # Custom duration metric
        duration = (time.time() - start_time) * 1000
        cloudwatch.put_metric_data(
            Namespace='MyApp/Lambda',
            MetricData=[
                {
                    'MetricName': 'ProcessingDuration',
                    'Value': duration,
                    'Unit': 'Milliseconds',
                    'Timestamp': datetime.utcnow()
                }
            ]
        )
```

### API Gateway Monitoring
```json
{
  "CloudWatchMetrics": {
    "4XXError": {
      "MetricName": "4XXError",
      "Namespace": "AWS/ApiGateway",
      "Dimensions": {
        "ApiName": "MyAPI",
        "Stage": "prod"
      }
    },
    "5XXError": {
      "MetricName": "5XXError",
      "Namespace": "AWS/ApiGateway"
    },
    "Latency": {
      "MetricName": "Latency",
      "Namespace": "AWS/ApiGateway"
    }
  }
}
```

## 🔔 Alerting Strategies

### Alert Severity Levels
- **Critical**: Immediate attention required (P1)
- **High**: Important issues (P2)
- **Medium**: Moderate issues (P3)
- **Low**: Minor issues (P4)
- **Info**: Informational alerts

### Alert Routing
```json
{
  "AlertRouting": {
    "Critical": {
      "SNS": "arn:aws:sns:us-east-1:123456789012:critical-alerts",
      "Actions": ["PagerDuty", "SMS", "Email"]
    },
    "High": {
      "SNS": "arn:aws:sns:us-east-1:123456789012:high-alerts",
      "Actions": ["Email", "Slack"]
    },
    "Medium": {
      "SNS": "arn:aws:sns:us-east-1:123456789012:medium-alerts",
      "Actions": ["Email"]
    }
  }
}
```

### Alert Fatigue Prevention
- **Meaningful Thresholds**: Set appropriate alarm thresholds
- **Alert Grouping**: Group related alerts
- **Escalation Policies**: Implement escalation procedures
- **Regular Review**: Review and tune alerts regularly

## 💰 Cost Optimization

### CloudWatch Pricing
- **Metrics**: $0.30 per metric per month (first 10,000 metrics)
- **Logs**: $0.50 per GB ingested, $0.03 per GB stored
- **Alarms**: $0.10 per alarm per month
- **Dashboards**: $3.00 per dashboard per month

### Cost Optimization Strategies
- **Log Retention**: Set appropriate retention periods
- **Metric Filtering**: Filter unnecessary log events
- **Custom Metrics**: Use sparingly, batch when possible
- **Dashboard Optimization**: Consolidate dashboards

## 🎯 Best Practices

### Logging Best Practices
- Use structured logging (JSON format)
- Include correlation IDs for tracing
- Set appropriate log levels
- Implement log retention policies
- Use log sampling for high-volume applications

### Metrics Best Practices
- Use meaningful metric names and dimensions
- Implement custom metrics for business KPIs
- Use metric math for calculated metrics
- Monitor both technical and business metrics
- Set up proper alerting thresholds

### Alerting Best Practices
- Alert on symptoms, not causes
- Use composite alarms for complex conditions
- Implement proper escalation procedures
- Test alert mechanisms regularly
- Document alert response procedures

### Dashboard Best Practices
- Design for your audience
- Use appropriate visualization types
- Include both technical and business metrics
- Implement drill-down capabilities
- Keep dashboards focused and relevant