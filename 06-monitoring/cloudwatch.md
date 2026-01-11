# CloudWatch

- Provides services to ingest, store and manage metrics
- It is a public service - provides public space endpoints
- Many services have native management plan integration with CloudWatch, for example EC2. Also, EC2 provides external gathered information only, for metrics from inside an EC2 we can use CloudWatch agent
- CloudWatch can be used from on-premises using the agent or the CloudWatch API
- CloudWatch stores data in a persistent way
- Data can be viewed from the console, CLI or API, but also CloudWatch also provides dashboards and anomaly detection
- CloudWatch Alarms: react to metrics, can be used to notify or to perform actions
- Instances in public subnet can connect to cloudwatch using Internet gateway and the instances in the private subnets can connect to the cloudwatch using Interface Endpoint.

## CloudWatch - Data

- **Namespace**: A namespace is a container for CloudWatch metrics. There is no default namespace. You must specify a namespace for each data point you publish to CloudWatch. The AWS namespaces typically use the following naming convention: AWS/**service**. For example, Amazon EC2 uses the AWS/EC2 namespace. 
- **Metric**:  A metric represents a time-ordered set of data points that are published to CloudWatch. Example of builtin metrics: `CPUUtilization`, `NetworkIn`, `DiskWriteBytes` for EC2. Every metric has a `MetricName`, a namespace and zero or more dimensions. e.g. CPUUtilization for AWS/EC2
    - **Time stamps**: Each metric data point must be associated with a time stamp. The time stamp can be up to two weeks in the past and up to two hours into the future. If you do not provide a time stamp, CloudWatch creates a time stamp for you based on the time the data point was received.
    **Metrics retention:**
    - 3 hours: High-resolution custom metrics with a period of less than 60 seconds are retained for 3 hours
    - 15 days: Data points with a period of 1 minute are retained for 15 days.
    - 63 days: Data points with a period of 5 minutes are retained for 63 days. 
    - 15 months: Data points with a period of 1 hour are available for 15 months (455 days)
    - As data ages, its aggregated and stored for longer period of time with less resolution
- **Dimension**: A dimension is a name/value pair that is part of the identity of a metric, example: a dimension is the way for `CPUUtilization` metric to be separated from one instance to another.
    - AWS services, such as Amazon EC2, CloudWatch can aggregate data across dimensions.
    - CloudWatch does not aggregate across dimensions for your custom metrics.
- **Resolution**: standard (60 second granularity) or high (1 second granularity). Every PutMetricData call for a custom metric is charged, so calling PutMetricData more often on a high-resolution metric can lead to higher charges. 
    - **Metric resolution**: minimum period that we can get one particular data point for
- **Statistics**: Statistics are metric data aggregations over specified periods of time. Aggregations are made using the namespace, metric name, dimensions, and the data point unit of measure, within the time period you specify.
    - **Units**: Each statistic has a unit of measure. Example units include Bytes, Seconds, Count, and Percent. You can specify a unit when you create a custom metric. If you do not specify a unit, CloudWatch uses None as the unit.
    - **Periods**:  A period is the length of time associated with a specific Amazon CloudWatch statistic. Periods are defined in numbers of seconds, and valid values for period are 1, 5, 10, 30, or any multiple of 60. When you retrieve statistics, you can specify a period, start time, and end time.
    - **Aggregation**: Amazon CloudWatch aggregates statistics according to the period length that you specify when retrieving statistics. Amazon CloudWatch doesn't differentiate the source of a metric. If you publish a metric with the same namespace and dimensions from different sources, CloudWatch treats this as a single metric. This can be useful for service metrics in a distributed, scaled system. For example, all the hosts in a web server application could publish identical metrics representing the latency of requests they are processing. CloudWatch treats these as a single metric, allowing you to get the statistics for minimum, maximum, average, and sum of all requests across your application.
- **Percentile**: A percentile indicates the relative standing of a value in a dataset. For example, the 95th percentile means that 95 percent of the data is lower than this value and 5 percent of the data is higher than this value. Percentiles help you get a better understanding of the distribution of your metric data.

## CloudWatch Alarms

- Alarm: watches a metric over a period of time
- States: `ALARM` or `OK` based on the value of a metric against a threshold over time
- Alarms can be configured with one or more actions, which can initiate actions on our behalf. Actions can be: send notification to an SNS topic, attempt an auto scaling policy modification or use Event Bridge to integrate with other services
- High resolution metrics can have high resolution alarms

## CloudWatch Logs

- CloudWatch Logs provides two type of functionalities: ingestion and subscription
- CloudWatch Logs is a public service designed to store, monitor and provide access logging data
- Can provide logging ingestion for AWS products natively, but also for on-premises, IOT or any application
- CloudWatch Agent: used to provide ingestion for custom applications
- CloudWatch can also ingest log streams from VPC Flow Logs or CloudTrail (account events and AWS API calls)
- CloudWatch Logs is regional service, certain global services send their logs to `us-east-1`
- Log events consist of 2 parts:
    - **Timestamp**
    - **Raw message**
- Log Events can be collected into Log Streams. Log Streams are sequence of log events sharing the same source
- Log Groups: are collection of Log Streams. We can set retention, permissions and encryption on the log groups. By default log groups store data indefinitely
- Metric Filter: can be defined on the log group and will look for pattern in the log events. Essentially creates a metric from the log streams by looking at occurrences of certain patterns defined by us (example: failed SSH logs in events)
- Export logs from CloudWatch:
    - **S3 Export**: we can create an export task (`Create-Export-Task`) which will take up to 12 hours. Its not real time. It is encrypted using SSE-S3.
    - **Subscription**: deliver logs in real time. We should create a subscription filter for the following destination: Kinesis Data Firehose (near real time), OpenSearch (ElasticSearch) using Lambda or custom Lambda, Kinesis Data Streams (any KCL consumer)
- Subscription filters can be used to create a logging aggregation architecture

## CloudWatch Dashboards

- A great way to setup dashboards for quick access to key metrics
- Dashboards are global
- Dashboards can include graphs from different regions
- We can change the time zone and time range of the dashboards
- We can setup automatic refresh (10s, 1m, 2m, 5m, 15m)
- Pricing:
    - 3 dashboards (up to 50 metrics) for free
    - $3/dashboard/month

## CloudWatch Synthetics Canary

- Synthetics Canary are configurable scripts that will monitor APIs and URLs
- These scripts meant to reproduce what a customer would do in order to find issues before the app is deployed to production
- They can be also used to check the availability and latency of our endpoints
- They can store load time data and screenshots of the UI
- They have integration with CloudWatch Alarms
- The scripts can be written in Node.js or Python
- Provides programmatic access to a headless Chrome browser
- They can be run once or on a regular basis
- Canary Blueprints:
    - Heartbeat Monitor: load URL, store screenshot and an HTTP archive file
    - API Canary: test basic read and write functions of a REST API
    - Broken Link Checker: check all links inside a page
    - Visual Monitoring: compare a screenshot taken during a canary run with a baseline screenshot
    - Canary Recorder: used with CloudWatch Synthetics Recorder - used to record actions on a website and automatically generate a test script for that
    - GUI Workflow Builder: verifies that actions can be taken on a webpage (example: test a webpage with a login form)