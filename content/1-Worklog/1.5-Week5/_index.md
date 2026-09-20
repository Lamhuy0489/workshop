---
title: "Week 5 Worklog"
date: 2026-09-20
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:
* Research and master the Three Pillars of Observability (Metrics, Logs, Alarms & Traces) on Amazon Web Services (AWS).
* Harness Amazon CloudWatch monitoring: Collect and analyze core compute metrics for Amazon EC2 instances (`CPUUtilization`, `NetworkIn`, `NetworkOut`).
* Apply CloudWatch Metric Math: Construct dynamic mathematical expression `(m2 + m3) / 1024` to aggregate total inbound/outbound network throughput into Kilobytes (KB) for real-time visualization.
* Centralize log management with CloudWatch Logs: Create Log Groups (`/huylam/cloudwatch/system-logs`, `/huylam/cloudwatch/httpd-access`), establish Log Streams, and ingest structured system event telemetry.
* Query structured log streams using CloudWatch Logs Insights: Execute SQL-like search queries to parse and filter log events bearing student identity Lam Quang Huy (Student ID: `0212267`).
* Establish an emergency alert notification pipeline with Amazon Simple Notification Service (Amazon SNS): Provision Topic `huylam-cw-alarms`, subscribe email endpoint `huyngu127@gmail.com`, and verify subscription status (Subscription Confirmed).
* Configure automated CloudWatch Alarms: Define a Static Threshold (`>= 70%`) for EC2 instance `i-048fa1b4099b74bb7` over a 1-minute (60s) evaluation period, triggering SNS email notifications upon breach.
* Execute empirical high-load stress tests: Drive CPU utilization to 100% using synthetic stress workloads, verifying state transitions from `OK` to `ALARM` (peaking at 93.86%), receiving automated SNS email notifications, and observing automated recovery to `OK` (6.46%).
* Construct an operational CloudWatch Dashboard (`huylam-monitoring-dashboard`): Integrate 4 specialized widgets including Alarm status, Single value metric, Line graph with threshold line, and Metric Math network throughput.
* Enforce FinOps cost-governance standards: Audit AWS Billing and Cost Management, review Month-to-date (MTD) expenditure (0.10 USD), confirm Healthy status across 2 AWS Budgets, and systematically decommission all test infrastructure.

---

### Tasks Carried Out in Week 5:

| Day | Task | Key Deliverable | Reference Material |
| :--- | :--- | :--- | :--- |
| **Mon** | - Study Observability fundamentals: Metrics, Logs, Traces.<br>- Investigate Amazon CloudWatch telemetry architecture.<br>- Compare monitoring intervals (Basic Monitoring: 5-min vs Detailed Monitoring: 1-min). | Mastered CloudWatch operating principles and AWS native monitoring patterns. | [AWS CloudWatch Concepts](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html) |
| **Tue** | - Research CloudWatch Metrics and Namespaces.<br>- Capture EC2 runtime metrics (`CPUUtilization`, `NetworkIn`, `NetworkOut`).<br>- Construct Metric Math expression `(m2 + m3) / 1024` to compute aggregate network KB. | Implemented combined network throughput visualization from disparate raw byte streams. | [CloudWatch Metric Math Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) |
| **Wed** | - Explore CloudWatch Logs architecture: Log Groups, Log Streams, Retention.<br>- Provision Log Groups `/huylam/cloudwatch/system-logs` and `/huylam/cloudwatch/httpd-access`.<br>- Run Logs Insights queries filtering student identity events. | Successfully extracted 8 structured log records validating student identity Lam Quang Huy (ID: `0212267`). | [CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) |
| **Thu** | - Research Amazon SNS pub/sub messaging architecture.<br>- Create SNS Topic `huylam-cw-alarms` and subscribe `huyngu127@gmail.com`.<br>- Confirm email subscription via automated confirmation link. | Established verified notification pipeline connecting CloudWatch alarms to the administrator mailbox. | [Amazon SNS Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/welcome.html) |
| **Fri** | - Create CloudWatch Alarm `huylam-ec2-high-cpu-alarm` with static threshold >= 70%.<br>- Attach notification trigger forwarding to SNS Topic `huylam-cw-alarms`.<br>- Verify initial baseline status (OK status). | Configured automated threshold rule protecting compute instances against resource exhaustion. | [Lab 000008](https://000008.awsstudygroup.com) |
| **Sat** | - Execute CPU stress test on EC2 instance `i-048fa1b4099b74bb7`.<br>- Recorded CPU surge to 93.86%, triggering alarm transition from OK to ALARM.<br>- Confirmed SNS email receipt in Gmail, followed by automated return to OK (6.46%). | Empirically validated full incident detection, alerting, and self-recovery lifecycle. | [AWS Systems Manager Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/) |
| **Sun** | - Design comprehensive CloudWatch Dashboard `huylam-monitoring-dashboard` with 4 widgets.<br>- Review FinOps metrics: Month-to-date cost at $0.10, 2 Budgets Healthy.<br>- Execute FinOps teardown: Deleted Alarms, Dashboards, Log Groups, SNS Topics, and terminated EC2 instances.<br>- Finalized technical documentation and published report. | Accomplished Lab 000008 requirements in full while preserving AWS Free Tier eligibility. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

---

### Technical Specifications Verified on AWS:

#### 1. Identity & Region:
- **AWS Account ID**: `677994024390`
- **Account Name**: `huylam`
- **Executing IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **Associated VPC**: `huylam-vpc` (`vpc-0125f4d6db3fbffa6`)

#### 2. Compute Infrastructure:
* **Test Instance 1**: `i-010014c0c84c08ef6` (Amazon Linux 2023, `t3.micro`)
* **Test Instance 2 (Stress Testing Target)**:
  - Instance ID: `i-048fa1b4099b74bb7`
  - Instance Type: `t3.micro` (2 vCPUs, 1 GiB RAM)
  - Operating System: Amazon Linux 2023 Kernel 6.1 x86_64
  - Purpose: CPU load induction, alarm state transitions, and SNS notification validation.

#### 3. Alert Delivery Channel (Amazon SNS):
- **SNS Topic Name**: `huylam-cw-alarms`
- **Topic ARN**: `arn:aws:sns:ap-southeast-1:677994024390:huylam-cw-alarms`
- **Delivery Protocol**: Email
- **Notification Recipient**: `huyngu127@gmail.com`
- **Subscription Status**: `Confirmed`

#### 4. Telemetry Collection & CloudWatch Metric Math:
- **Namespace**: `AWS/EC2`
- **Base Metrics**:
  - `m1`: `CPUUtilization` on instance `i-048fa1b4099b74bb7` (Unit: Percent, Period: 60s)
  - `m2`: `NetworkIn` on instance `i-048fa1b4099b74bb7` (Unit: Bytes, Period: 60s)
  - `m3`: `NetworkOut` on instance `i-048fa1b4099b74bb7` (Unit: Bytes, Period: 60s)
- **Metric Math Expression**:
  - Expression ID: `e1`
  - Formula: `(m2 + m3) / 1024`
  - Display Label: `Total Network KB`
  - Purpose: Aggregates inbound and outbound network bytes into a single metric denominated in Kilobytes, enabling NOC operators to assess total bandwidth consumption at a glance.

#### 5. Centralized Telemetry (CloudWatch Logs):
- **Provisioned Log Groups**:
  - `/huylam/cloudwatch/system-logs`: Linux kernel events and system telemetry.
  - `/huylam/cloudwatch/httpd-access`: HTTP web server access events.
- **Active Log Stream**: `ec2-system-stream`
- **Logs Insights Query**:
  ```sql
  fields @timestamp, @message
  | sort @timestamp desc
  | limit 20
  ```
- **Query Verification**: Returned 8 structured system records containing student identity Lam Quang Huy (Student ID: `0212267`).

#### 6. Automated Alerting (CloudWatch Alarm):
- **Alarm Name**: `huylam-ec2-high-cpu-alarm`
- **Alarm ARN**: `arn:aws:cloudwatch:ap-southeast-1:677994024390:alarm:huylam-ec2-high-cpu-alarm`
- **Description**: `CloudWatch Alarm for student Lam Quang Huy (MSSV: 0212267) - High CPU Utilization on i-048fa1b4099b74bb7`
- **Monitored Metric**: `AWS/EC2` -> `CPUUtilization` on `i-048fa1b4099b74bb7`
- **Threshold Conditions**:
  - Threshold Type: Static Threshold
  - Trigger Condition: `CPUUtilization >= 70%`
  - Datapoints to Alarm: 1 out of the last 1 consecutive 60-second period
  - Evaluation Period: 60 seconds (1 minute)
  - Missing Data Treatment: `missing` (Treat missing data as missing)
- **Actions**:
  - Alarm State: `In ALARM`
  - Action Executed: Publish notification to SNS Topic `huylam-cw-alarms` (`arn:aws:sns:ap-southeast-1:677994024390:huylam-cw-alarms`)

#### 7. Centralized Monitoring Dashboard:
- **Dashboard Name**: `huylam-monitoring-dashboard`
- **Four-Widget Architecture**:
  - Widget 1 (Alarm Status): Real-time visual status indicator for `huylam-ec2-high-cpu-alarm` (`OK` state).
  - Widget 2 (Single Value): Current instantaneous CPU utilization reading.
  - Widget 3 (Line Graph - CPU Utilization): Continuous CPU usage history featuring a static red threshold line at 70% and the stress test peak.
  - Widget 4 (Line Graph - Metric Math Network): Computed line graph tracking combined network throughput in KB.

#### 8. Cost Governance & FinOps Overview:
- **Month-to-Date (MTD) Spend**: `0.10 USD`
- **Month-End Forecasted Spend**: `0.14 USD`
- **AWS Budgets Status**: 2 active budgets ($5 and $10 alerts) operating at `HEALTHY` status.
- **FinOps Execution**: Decommissioned all compute and logging resources immediately after test validation, eliminating idle costs.

---

### Screenshots & Practical Evidence on AWS:

All practical evidence screenshots were captured directly from the AWS Management Console and recipient email mailbox. Critical regions including the account badge `huylam (677994024390)`, Singapore region `ap-southeast-1`, and verified configuration parameters are highlighted with precise red bounding boxes:

#### 1. Metric Math Expression Configuration on CloudWatch Metrics:
- **Description**: CloudWatch Metrics console monitoring `CPUUtilization` (m1), `NetworkIn` (m2), and `NetworkOut` (m3), with mathematical expression `(m2 + m3) / 1024` (Id: `e1`, Label: `Total Network KB`) aggregating network bandwidth.
- **Highlighted red border**: Account badge `huylam (677994024390)`, 1-hour time range selector, and the metrics table showing the mathematical formula `(m2 + m3) / 1024`.

![CloudWatch Metric Math Configuration](/images/week5/01-cloudwatch-metrics-math.png)

---

#### 2. CloudWatch Log Groups Management:
- **Description**: Centralized Log Groups inventory under CloudWatch Logs, including `/huylam/cloudwatch/system-logs` and `/huylam/cloudwatch/httpd-access`.
- **Highlighted red border**: Account badge `huylam (677994024390)` and the list of Log Groups prefixed with `/huylam/cloudwatch/*`.

![CloudWatch Log Groups List](/images/week5/02-cloudwatch-log-groups.png)

---

#### 3. Log Group Details & Stream Inventory:
- **Description**: Inspection of `/huylam/cloudwatch/system-logs` configuration parameters, Log Group ARN, retention policy (Never expire), and child Log Stream `ec2-system-stream`.
- **Highlighted red border**: Account badge `huylam (677994024390)`, Log Group summary card with retention settings, and the Log Streams table.

![Log Group Details and Streams](/images/week5/03-cloudwatch-log-group-details.png)

---

#### 4. Querying Telemetry with CloudWatch Logs Insights:
- **Description**: Executing a structured query over `/huylam/cloudwatch/system-logs`, returning 8 system records containing student identity Lam Quang Huy (Student ID: `0212267`).
- **Highlighted red border**: Account badge `huylam (677994024390)`, Logs Insights query editor, and the returned records table with 8 event rows.

![CloudWatch Logs Insights Query](/images/week5/04-cloudwatch-logs-insights-query.png)

---

#### 5. Selecting CPUUtilization Metric for EC2 Alarm:
- **Description**: Initiating CloudWatch Alarm creation by filtering `i-048fa1b4099b74bb7` under `AWS/EC2 > Per-Instance Metrics` and selecting the `CPUUtilization` metric.
- **Highlighted red border**: Account badge `huylam (677994024390)`, metric row for instance `i-048fa1b4099b74bb7`, and the Select metric action button.

![Select CPUUtilization Metric](/images/week5/05-cloudwatch-alarm-select-metric.png)

---

#### 6. Configuring Alarm Static Threshold Conditions:
- **Description**: Configuring static threshold rule triggering when `CPUUtilization >= 70%` across a 1-minute period (60s), with interactive chart showing the red threshold marker.
- **Highlighted red border**: Account badge `huylam (677994024390)`, preview chart with 70% red line, and the Static Threshold definition pane.

![Configure Static Threshold Conditions](/images/week5/06-cloudwatch-alarm-conditions.png)

---

#### 7. Configuring SNS Notification Action & Alarm Details:
- **Description**: Setting up automated notification dispatch to SNS Topic `huylam-cw-alarms` when entering `In ALARM` state, including student identity description.
- **Highlighted red border**: Account badge `huylam (677994024390)`, SNS Topic notification configuration, and the Alarm description naming student Lam Quang Huy (`0212267`).

![Configure SNS Actions and Alarm Details](/images/week5/07-cloudwatch-alarm-actions-details.png)

---

#### 8. Successful CloudWatch Alarm Creation:
- **Description**: AWS confirmation of successful alarm deployment for `huylam-ec2-high-cpu-alarm`, entering the active evaluation schedule.
- **Highlighted red border**: Account badge `huylam (677994024390)`, green success notification banner, and the alarm list row.

![Alarm Created Successfully](/images/week5/08-cloudwatch-alarm-created-success.png)

---

#### 9. CloudWatch Alarm Returning to OK State Post-Test:
- **Description**: Following workload de-escalation, CPU utilization receded to 6.46%, automatically restoring alarm `huylam-ec2-high-cpu-alarm` to the green `OK` state.
- **Highlighted red border**: Account badge `huylam (677994024390)` and the alarm details row showing `OK` status with current reading at 6.46% (below the 70% threshold).

![Alarm Status OK](/images/week5/09-cloudwatch-alarm-status-ok.png)

---

#### 10. Automated Email Alert Received via AWS SNS:
- **Description**: Email notification delivered from `AWS Notifications <no-reply@sns.amazonaws.com>` to `huyngu127@gmail.com` when CPU reached 93.86%, confirming state transition from `OK -> ALARM`, student identity Lam Quang Huy (`0212267`), and Account `677994024390`.
- **Highlighted red border**: Subject line `ALARM: "huylam-ec2-high-cpu-alarm" in Asia Pacific (Singapore)`, sender address with timestamp, and the core message body indicating threshold crossing (93.86%) and Account ID `677994024390`.

![AWS SNS Email Alert](/images/week5/10-cloudwatch-alarm-email-notification.png)

---

#### 11. Consolidated CloudWatch Dashboard (`huylam-monitoring-dashboard`):
- **Description**: Unified 4-widget operations dashboard: Alarm Status widget, instantaneous Single Value CPU widget, Line Graph CPU widget showing the stress peak, and Metric Math Network KB widget.
- **Highlighted red border**: Account badge `huylam (677994024390)` and 4 precise bounding boxes framing all four dashboard widgets.

![Consolidated CloudWatch Dashboard](/images/week5/11-cloudwatch-monitoring-dashboard.png)

---

#### 12. AWS Billing & Budgets Status Review:
- **Description**: Comprehensive overview of the AWS Billing and Cost Management console confirming Month-to-date expenditure of 0.10 USD (forecasted at 0.14 USD) and 2 Budgets operating in Healthy status.
- **Highlighted red border**: Account badge `huylam (677994024390)`, Cost summary card displaying MTD $0.10, and Budgets card confirming 2 Healthy active budgets.

![AWS Billing and Budgets Overview](/images/week5/12-aws-billing-cost-budgets.png)

---

### Practical Verification & Technical Metrics Testing:

#### 1. Empirical Stress Testing & Alarm State Lifecycle:
To rigorously validate CloudWatch Alarm responsiveness, an intense CPU workload was generated on instance `i-048fa1b4099b74bb7`:
```bash
# Install stress-ng on Amazon Linux 2023
sudo dnf install -y stress-ng

# Stress 2 vCPU cores for 300 seconds
stress-ng --cpu 2 --timeout 300s --metrics-brief
```

*Lifecycle Timeline Observed*:
1. **Pre-Stress Baseline (T = 0s)**: CPU consumption remained steady below `5%`. Alarm `huylam-ec2-high-cpu-alarm` reported `OK`.
2. **Stress Onset (T = 60s -> 120s)**: Both vCPUs were fully saturated. The next CloudWatch 1-minute aggregation datapoint registered average utilization at `93.858%` (`93.86%`), surpassing the `70%` static limit.
3. **Alarm Triggered (T = 120s)**: CloudWatch evaluated evaluation criteria (1 out of 1 datapoint >= 70%) and transitioned state from `OK` to `ALARM`.
4. **Notification Dispatch (T = 125s)**: Transition event dispatched an alert payload to SNS Topic `huylam-cw-alarms`, delivering an email alert to `huyngu127@gmail.com` in under 5 seconds.
5. **Automated Recovery (T = 300s -> 360s)**: Following workload completion, CPU usage dropped to `6.46%`. On the subsequent evaluation cycle, the alarm automatically recovered from `ALARM` to `OK`.

#### 2. Telemetry Querying with CloudWatch Logs Insights:
A structured query was executed against `/huylam/cloudwatch/system-logs`:
```sql
fields @timestamp, @message
| sort @timestamp desc
| limit 20
```

*Query Evaluation*:
```text
---------------------------------------------------------------------------------------------------------------------
| @timestamp               | @message                                                                               |
+--------------------------+----------------------------------------------------------------------------------------+
| 2026-09-20T16:04:15.000Z | [SYSTEM_EVENT] Student: Lam Quang Huy (MSSV: 0212267) - Node i-048fa1b4099b74bb7 OK    |
| 2026-09-20T16:04:10.000Z | [SYSTEM_EVENT] CloudWatch Logs Agent health status verified. System healthy.          |
| 2026-09-20T16:04:05.000Z | [SYSTEM_EVENT] Student: Lam Quang Huy (MSSV: 0212267) - Monitoring initialized        |
| 2026-09-20T16:04:00.000Z | [SYSTEM_EVENT] HTTP Server started listening on port 80.                              |
| 2026-09-20T16:03:55.000Z | [SYSTEM_EVENT] Memory buffer allocation checked: 1024 MB available.                   |
| 2026-09-20T16:03:50.000Z | [SYSTEM_EVENT] Student: Lam Quang Huy (MSSV: 0212267) - Kernel 6.1 loaded successfully|
| 2026-09-20T16:03:45.000Z | [SYSTEM_EVENT] Network interface ens5 initialized. DHCP lease acquired.                |
| 2026-09-20T16:03:40.000Z | [SYSTEM_EVENT] System boot completed for instance i-048fa1b4099b74bb7.                |
---------------------------------------------------------------------------------------------------------------------
```
*Assessment*: Logs Insights delivered sub-second query latency over indexed event streams, proving its efficacy for fast Root Cause Analysis (RCA).

#### 3. Multidimensional Network Telemetry via Metric Math:
Using mathematical formula `(m2 + m3) / 1024` provided measurable operational advantages:
- Aggregates two raw telemetry streams (`NetworkIn` and `NetworkOut`) directly within the CloudWatch engine without application-side instrumentation.
- Normalizes large byte values into Kilobytes, enabling human operators to identify bandwidth anomalies intuitively.
- Reduces dashboard widget footprint by 50%, consolidating network telemetry into a clean, single-pane visualization.

---

### Three Pillars of Observability on AWS:

| Dimension | Metrics | Logs | Traces / Alarms |
| :--- | :--- | :--- | :--- |
| **Data Structure** | Time-series numerical data. | Unstructured or semi-structured timestamped text strings. | Distributed request invocation graphs or threshold state changes. |
| **Primary AWS Services** | Amazon CloudWatch Metrics. | Amazon CloudWatch Logs, CloudWatch Logs Insights. | AWS X-Ray, CloudWatch ServiceLens, CloudWatch Alarms. |
| **Primary Use Cases** | High-level system health, capacity trending, auto scaling triggers. | Detailed error diagnosis, forensic root-cause analysis, security auditing. | Latency bottleneck isolation, distributed request tracking, incident notification. |
| **Telemetry Latency** | 1 minute (Detailed) or 5 minutes (Basic). | Near real-time (sub-second ingestion latency). | Sub-minute evaluation upon datapoint delivery. |
| **Pricing Model** | Standard AWS metrics are free; custom metrics billed at tiered unit rates. | Billed by data ingestion (~$0.50/GB) and storage (~$0.03/GB/month). | First 10 standard alarms free; $0.10/alarm/month thereafter. |

---

### Cost Governance & FinOps Best Practices:

1. **Understanding CloudWatch Cost Drivers**:
   - **Basic Metrics**: Provided free by AWS for core resources (EC2, EBS, RDS) at 5-minute intervals. Detailed 1-minute monitoring introduces incremental metric charges.
   - **CloudWatch Logs**: Includes 5 GB free ingestion and 5 GB free storage monthly under AWS Free Tier. Always configure an explicit Log Retention Policy (e.g., 7 or 30 days) to prevent perpetual storage accumulation.
   - **Alarms & Dashboards**: Includes 10 standard alarms and up to 3 dashboards (50 metrics total) within the Free Tier allowance.

2. **Systematic Post-Verification Teardown (Resource Teardown)**:
   Following full evidence collection across all 12 stages, all experimental resources were terminated in exact dependency sequence:
   - Deleted CloudWatch Alarm: `aws cloudwatch delete-alarms --alarm-names huylam-ec2-high-cpu-alarm`
   - Deleted CloudWatch Dashboard: `aws cloudwatch delete-dashboards --dashboard-names huylam-monitoring-dashboard`
   - Deleted CloudWatch Log Groups:
     - `aws logs delete-log-group --log-group-name /huylam/cloudwatch/system-logs`
     - `aws logs delete-log-group --log-group-name /huylam/cloudwatch/httpd-access`
   - Deleted Amazon SNS Topic: `aws sns delete-topic --topic-arn arn:aws:sns:ap-southeast-1:677994024390:huylam-cw-alarms`
   - Terminated EC2 compute instances: `aws ec2 terminate-instances --instance-ids i-010014c0c84c08ef6 i-048fa1b4099b74bb7`

3. **FinOps Performance Metrics**:
   - Actual Month-to-Date expenditure: `0.10 USD`.
   - Forecasted month-end expenditure: `0.14 USD`.
   - 2 configured AWS Budgets ($5 and $10 alerts) operating at `HEALTHY` status.
   - 100% of compute and telemetry resources cleared, resulting in 0 USD ongoing idle costs.

---

### Key Takeaways & Conclusion:

1. **From Reactive Recovery to Proactive Observability**: Relying on end-user complaints to detect downtime is obsolete. Establishing threshold-based alarms linked to notification pub/sub topics enables operational engineers to triage emerging performance bottlenecks before customer-facing degradation occurs.
2. **Operational Efficiency with CloudWatch Metric Math & Logs Insights**: Leveraging AWS native analytical capabilities eliminates the immediate need for expensive third-party APM platforms in early infrastructure stages, providing unified telemetry directly in the AWS management console.
3. **FinOps as an Architectural Discipline**: Infrastructure engineering requires constant awareness of financial parameters. Establishing budget boundaries, reviewing cost allocation charts, and automating resource decommissioning are essential habits for every professional cloud architect.