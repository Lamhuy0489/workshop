---
title: "Week 7 Worklog"
date: 2026-09-21
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:
* Master **Infrastructure as Code (IaC)** principles on AWS using the **AWS CloudFormation** service.
* Construct a standardized **CloudFormation Template** in YAML format, implementing a declarative structure with `AWSTemplateFormatVersion`, `Description`, `Parameters`, `Resources`, and `Outputs`.
* Implement dynamic runtime resolution of the latest Amazon Linux 2023 AMI using the SSM Parameter Store type `AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>` with path `/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64`.
* Automate web server bootstrapping via Base64-encoded `UserData` script, installing Apache (`httpd`) and rendering a modern responsive interface identifying student Lam Quang Huy (Student ID: `0212267`), Class 67CS, Hanoi University of Civil Engineering (HUCE).
* Configure an EC2 **Security Group** allowing Inbound HTTP traffic on port 80 from anywhere (`0.0.0.0/0`) and unrestricted Outbound traffic for software repository access.
* Provision and manage the lifecycle of the CloudFormation Stack `huylam-cfn-stack`, tracking deployment milestones through Stack Events until reaching the `CREATE_COMPLETE` state.
* Inspect and verify Stack Outputs including Public IPv4 `47.129.129.6`, Security Group ID `sg-059146261d1b7c5eb`, Stack Name, and direct HTTP access URL.
* Validate web server responsiveness and availability through live browser testing over public Internet.
* Execute **CloudFormation Drift Detection** to verify alignment between real-world infrastructure state and the template specification.
* Maintain cloud financial governance (**FinOps**): Execute a clean, single-action FinOps Teardown by deleting the stack, releasing EC2 and Security Group resources to preserve the AWS Free Tier budget (0 USD).

---

### Tasks Executed in Week 7:

| Day | Task | Key Outcome | Reference Material |
| :--- | :--- | :--- | :--- |
| **Mon** | - Research Infrastructure as Code (IaC) principles.<br>- Compare manual console provisioning with IaC: repeatability, consistency, versioning, and automation.<br>- Explore AWS CloudFormation architecture. | Mastered CloudFormation Engine mechanics and translation of declarative templates into real AWS resources. | [AWS CloudFormation Concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html) |
| **Tue** | - Research CloudFormation template anatomy (YAML format).<br>- Study intrinsic functions: `!Ref`, `!Sub`, `!GetAtt`.<br>- Configure parameter specifications with constraints, defaults, and AllowedValues. | Mastered template parameterization for cross-environment reusability. | [CloudFormation Template Anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html) |
| **Wed** | - Research dynamic SSM Parameter Store integration in CloudFormation.<br>- Configure dynamic AMI resolution for Amazon Linux 2023 avoiding hardcoded AMI IDs.<br>- Author template file `huylam-cfn-week7.yaml`. | Optimized template portability across AWS Regions regardless of AMI ID variations. | [AWS SSM Parameter Types](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#aws-ssm-parameter-types) |
| **Thu** | - Author `UserData` bootstrap script for automated Apache (`httpd`) installation.<br>- Build responsive HTML template with student identification details.<br>- Define Security Group and EC2 Instance resources tagged with `Project = FCJ-Bootcamp-2026`. | Completed production-ready IaC template ready for live cloud deployment. | [EC2 User Data in CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html#cfn-ec2-instance-userdata) |
| **Fri** | - Provision CloudFormation Stack `huylam-cfn-stack` in region `ap-southeast-1`.<br>- Supply input parameters: `EnvironmentName`, `InstanceType` (`t3.micro`), `StudentID`, `StudentName`.<br>- Monitor live deployment events via Stack Events stream. | All infrastructure resources successfully provisioned to `CREATE_COMPLETE` within 25 seconds. | [Working with Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html) |
| **Sat** | - Inspect Stack details: Stack Info, Events, Resources, Outputs, and Template.<br>- Extract public IP `47.129.129.6` from Outputs tab.<br>- Access website via web browser to verify application functionality and HTTP 200 status. | 100% verified student web server running smoothly on CloudFormation-provisioned infrastructure. | [Viewing Stack Outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-view-stack-data-resources.html) |
| **Sun** | - Execute **CloudFormation Drift Detection** on `huylam-cfn-stack`.<br>- Audit FinOps cloud expenditures ensuring AWS Free Tier compliance.<br>- Execute FinOps Teardown by deleting the stack, releasing EC2 and Security Group.<br>- Compile technical lab documentation and update Week 7 Worklog. | Successfully achieved all Week 7 objectives with high precision while maintaining 0 USD incurred cost. | [Detecting Drift on Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html) |

---

### Verified Technical Specifications on AWS:

#### 1. Identity & Region:
- **AWS Account ID**: `677994024390`
- **Account Name**: `huylam`
- **IAM Principal**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **Availability Zone**: `ap-southeast-1c`
- **VPC**: Default VPC (`vpc-0c84feaf395ece4dd`)
- **Subnet**: `subnet-0bba3228d80514181` (Public Subnet)

#### 2. CloudFormation Stack Specification:
- **Stack Name**: `huylam-cfn-stack`
- **Stack ID**: `arn:aws:cloudformation:ap-southeast-1:677994024390:stack/huylam-cfn-stack/54395360-b520-11f1-90f4-0a4ed60f7479`
- **Creation Time**: `2026-09-20 18:23:08 UTC`
- **Stack Status**: `CREATE_COMPLETE`
- **Deployment Duration**: Approximately 25 seconds from template submission
- **Rollback Configuration**: Default (Rollback on failure)
- **Termination Protection**: Disabled

#### 3. Stack Parameters:
- **EnvironmentName**: `FCJ-Bootcamp-2026`
- **InstanceType**: `t3.micro` (AWS Free Tier eligible)
- **LatestAmiId**: `/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64`
  - Resolved Dynamic AMI: `ami-085b17e53d4c8f0cb` (Amazon Linux 2023 64-bit x86_64)
- **StudentID**: `0212267`
- **StudentName**: `Lam Quang Huy`

#### 4. EC2 Instance Provisioned via IaC:
- **Instance Name Tag**: `huylam-cfn-webserver`
- **Logical Resource ID**: `WebServerInstance`
- **Physical Resource ID**: `i-0b328b3c9bf942cd7`
- **Resource Type**: `AWS::EC2::Instance`
- **Instance State**: `running`
- **Public IPv4 Address**: `47.129.129.6`
- **Private IPv4 Address**: `172.31.15.0`
- **Public DNS**: `ec2-47-129-129-6.ap-southeast-1.compute.amazonaws.com`
- **Operating System**: Amazon Linux 2023
- **Tags Attached**:
  - `Name`: `huylam-cfn-webserver`
  - `Project`: `FCJ-Bootcamp-2026`
  - `StudentID`: `0212267`
  - `aws:cloudformation:stack-name`: `huylam-cfn-stack`
  - `aws:cloudformation:logical-id`: `WebServerInstance`

#### 5. Security Group Provisioned via IaC:
- **Logical Resource ID**: `WebServerSecurityGroup`
- **Physical Resource ID**: `huylam-cfn-stack-WebServerSecurityGroup-oatvCIRD2205`
- **Security Group ID**: `sg-059146261d1b7c5eb`
- **Resource Type**: `AWS::EC2::SecurityGroup`
- **Inbound Rules**: Port 80 (HTTP) from `0.0.0.0/0` (Internet)
- **Outbound Rules**: All traffic (`0.0.0.0/0`) for package updates and web service initialization

#### 6. Stack Outputs:
- **WebServerPublicIp**: `47.129.129.6` - Public IPv4 address of the web server
- **WebServerUrl**: `http://47.129.129.6` - HTTP URL to access the deployed website
- **SecurityGroupId**: `huylam-cfn-stack-WebServerSecurityGroup-oatvCIRD2205` - Created Security Group ID
- **StackName**: `huylam-cfn-stack` - CloudFormation Stack identifier

#### 7. Drift Detection Results:
- **Drift Detection ID**: `8240c5e0-b520-11f1-854d-0205312630e5`
- **Detection Timestamp**: `2026-09-20 18:29:44 UTC`
- **Drift Status**: Completed
- **Security Group Drift**: `IN_SYNC` (Perfect alignment with template)
- **EC2 Instance Drift**: `MODIFIED` (Captures runtime networking additions like dynamic Public IP and ENI attachments)

---

### CloudFormation Template Definition:

The complete infrastructure stack is codified in the YAML template `huylam-cfn-week7.yaml`:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: >
  AWS CloudFormation Lab 000037 - Infrastructure as Code (IaC)
  Student: Lam Quang Huy | Student ID: 0212267 | Class: 67CS - HUCE
  FCJ Cloud Journey Bootcamp 2026

Parameters:
  EnvironmentName:
    Type: String
    Default: FCJ-Bootcamp-2026
    Description: Deployment environment tag name

  StudentName:
    Type: String
    Default: Lam Quang Huy
    Description: Student full name

  StudentID:
    Type: String
    Default: "0212267"
    Description: Student ID number

  InstanceType:
    Type: String
    Default: t3.micro
    AllowedValues:
      - t2.micro
      - t3.micro
    Description: Amazon EC2 instance type (Free Tier eligible)

  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64'
    Description: Automatically resolve the latest Amazon Linux 2023 AMI via AWS Systems Manager Parameter Store

Resources:
  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable HTTP access from anywhere for HuyLam Web Server
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Sub '${AWS::StackName}-web-sg'
        - Key: Project
          Value: !Ref EnvironmentName
        - Key: StudentID
          Value: !Ref StudentID

  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !Ref LatestAmiId
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          dnf update -y
          dnf install -y httpd
          systemctl start httpd
          systemctl enable httpd
          cat <<EOF > /var/www/html/index.html
          <!DOCTYPE html>
          <html lang="vi">
          <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>AWS CloudFormation Lab 000037 - ${StudentName}</title>
            <style>
              body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #0f172a; color: #f8fafc; margin: 0; padding: 40px 20px; display: flex; justify-content: center; align-items: center; min-height: 80vh; }
              .card { background: #1e293b; border-radius: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); padding: 36px; max-width: 650px; width: 100%; border: 1px solid #334155; }
              h1 { color: #38bdf8; font-size: 24px; margin-top: 0; border-bottom: 2px solid #334155; padding-bottom: 12px; }
              .badge { display: inline-block; background-color: #0284c7; color: white; padding: 4px 10px; border-radius: 6px; font-weight: 600; font-size: 13px; margin-bottom: 16px; }
              .info-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #334155; }
              .label { color: #94a3b8; font-weight: 500; }
              .value { font-weight: 600; color: #f1f5f9; }
              .success { color: #4ade80; font-weight: bold; }
              .footer { margin-top: 24px; text-align: center; color: #64748b; font-size: 13px; }
            </style>
          </head>
          <body>
            <div class="card">
              <span class="badge">AWS CloudFormation IaC</span>
              <h1>Web Server Deployed via CloudFormation</h1>
              <div class="info-row"><span class="label">Hoc vien:</span><span class="value">${StudentName}</span></div>
              <div class="info-row"><span class="label">MSSV:</span><span class="value">${StudentID}</span></div>
              <div class="info-row"><span class="label">Lop:</span><span class="value">67CS - Truong DH Xay dung Ha Noi (HUCE)</span></div>
              <div class="info-row"><span class="label">Stack Name:</span><span class="value">${AWS::StackName}</span></div>
              <div class="info-row"><span class="label">Region:</span><span class="value">${AWS::Region}</span></div>
              <div class="info-row"><span class="label">Du an:</span><span class="value">${EnvironmentName}</span></div>
              <div class="info-row"><span class="label">Trang thai:</span><span class="value success">SUCCESSFULLY PROVISIONED</span></div>
              <div class="footer">FCJ Cloud Journey Bootcamp 2026 - Infrastructure as Code Module</div>
            </div>
          </body>
          </html>
          EOF
      Tags:
        - Key: Name
          Value: huylam-cfn-webserver
        - Key: Project
          Value: !Ref EnvironmentName
        - Key: StudentID
          Value: !Ref StudentID

Outputs:
  WebServerPublicIp:
    Description: Public IPv4 address of the web server
    Value: !GetAtt WebServerInstance.PublicIp

  WebServerUrl:
    Description: HTTP URL to access the deployed website
    Value: !Sub 'http://${WebServerInstance.PublicIp}'

  SecurityGroupId:
    Description: ID of the Security Group created
    Value: !Ref WebServerSecurityGroup

  StackName:
    Description: Name of the deployed CloudFormation Stack
    Value: !Ref 'AWS::StackName'
```

---

### Deployment Proof Screenshots from AWS:

All proof screenshots below were captured directly from the live AWS Management Console and browser sessions of student **Lam Quang Huy (Student ID: 0212267)**. Key areas including the account badge `huylam (677994024390)`, region `ap-southeast-1`, and critical technical details are highlighted with precise red bounding boxes:

#### 1. Initial CloudFormation Stacks List:
- **Description**: CloudFormation console in region `ap-southeast-1` before deployment. The initial state is clean (`Stacks (0)`, `No stacks to display`) with the prominent orange `Create stack` button ready.
- **Highlighted Area**: Account badge `huylam (677994024390)`, header toolbar `Stacks (0)`, and central empty state container.

![CloudFormation Stacks List Initial](/images/week7/01-cfn-stacks-list-initial.png)

---

#### 2. Template Upload via Console:
- **Description**: Step 1 (Create stack - Prerequisite - Prepare template) with `Template is ready` and `Upload a template file` selected, confirming successful upload of `huylam-cfn-week7.yaml`.
- **Highlighted Area**: Account badge `huylam (677994024390)` and template specification card showing the uploaded file ready for next step.

![Create Stack Upload Template](/images/week7/02-cfn-create-stack-upload-template.png)

---

#### 3. Specify Stack Details and Parameters:
- **Description**: Step 2 (Specify stack details) entering Stack Name `huylam-cfn-stack` and configuring parameters: `EnvironmentName` (`FCJ-Bootcamp-2026`), `InstanceType` (`t3.micro`), `LatestAmiId` SSM path, `StudentID` (`0212267`), and `StudentName` (`Lam Quang Huy`).
- **Highlighted Area**: Account badge `huylam (677994024390)`, `Provide a stack name` card, and `Parameters` card enclosing all 5 defined parameters.

![Specify Stack Details](/images/week7/03-cfn-specify-stack-details.png)

---

#### 4. Stack Creation in Progress:
- **Description**: Stack overview immediately after submission. Stack status displays `CREATE_IN_PROGRESS` with timestamp `2026-09-21 01:23:08 UTC+0700`.
- **Highlighted Area**: Account badge `huylam (677994024390)` and overview card highlighting `CREATE_IN_PROGRESS`.

![Stack Create In Progress](/images/week7/04-cfn-stack-create-in-progress.png)

---

#### 5. Live Web Server Browser Verification:
- **Description**: Web browser accessing public IPv4 `http://47.129.129.6`. The page displays the custom HTML styled card dynamically generated by `UserData` identifying Lam Quang Huy, 0212267, 67CS, huylam-cfn-stack, ap-southeast-1, and status `SUCCESSFULLY PROVISIONED`.
- **Highlighted Area**: Browser address bar showing `http://47.129.129.6/` and student confirmation card.

![Web Server Browser Verification](/images/week7/05-cfn-webserver-browser-verification.png)

---

#### 6. Stack Creation Completed:
- **Description**: Stack info tab after successful completion. Status is updated to `CREATE_COMPLETE` at `2026-09-21 01:23:33 UTC+0700` with the student description.
- **Highlighted Area**: Account badge `huylam (677994024390)` and Stack info panel confirming `CREATE_COMPLETE`.

![Stack Info Create Complete](/images/week7/06-cfn-stack-info-create-complete.png)

---

#### 7. Complete Stack Provisioning Events:
- **Description**: Events tab displaying real-time resource provisioning milestones: Security Group creation, EC2 Instance creation, and stack state transition to `CREATE_COMPLETE`.
- **Highlighted Area**: Account badge `huylam (677994024390)` and event log table confirming all resources provisioned.

![Stack Events All Complete](/images/week7/07-cfn-stack-events-all-complete.png)

---

#### 8. Physical Resources Inventory:
- **Description**: Resources tab detailing the 2 provisioned cloud resources: `WebServerInstance` (`i-0b328b3c9bf942cd7`) and `WebServerSecurityGroup` (`huylam-cfn-stack-WebServerSecurityGroup-oatvCIRD2205`).
- **Highlighted Area**: Account badge `huylam (677994024390)` and `Resources (2)` inventory table.

![Stack Resources List](/images/week7/08-cfn-stack-resources-list.png)

---

#### 9. Stack Outputs Verification:
- **Description**: Outputs tab listing computed stack values: `SecurityGroupId`, `StackName`, `WebServerPublicIp` (`47.129.129.6`), and `WebServerUrl` (`http://47.129.129.6`).
- **Highlighted Area**: Account badge `huylam (677994024390)` and Outputs table with all 4 key-value pairs.

![Stack Outputs Values](/images/week7/09-cfn-stack-outputs-values.png)

---

#### 10. Stack Template Definition:
- **Description**: Template tab verifying the stored and rendered YAML template, validating the parameters and resource configuration.
- **Highlighted Area**: Account badge `huylam (677994024390)` and template code preview container.

![Stack Template YAML](/images/week7/10-cfn-stack-template-yaml.png)

---

#### 11. Initiating Drift Detection:
- **Description**: Stack actions dropdown menu highlighting `Detect drift` to trigger configuration drift detection against the baseline template.
- **Highlighted Area**: Account badge `huylam (677994024390)` and `Stack actions` menu with `Detect drift` selected.

![Stacks List Actions Menu](/images/week7/11-cfn-stacks-list-actions-menu.png)

---

#### 12. Drift Detection Execution and Status:
- **Description**: Confirmation alert `Drift detection initiated for huylam-cfn-stack` with detection ID `8240c5e0-b520-11f1-854d-0205312630e5` and completed drift audit status.
- **Highlighted Area**: Account badge `huylam (677994024390)`, system alert notification, and drift status section.

![Stack Drift Detection](/images/week7/12-cfn-stack-drift-detection.png)

---

### Key Takeaways and Insights:
1. **Mastery of Infrastructure as Code (IaC)**: Fundamental paradigm shift from manual console provisioning to version-controlled, auditable, and automated infrastructure delivery.
2. **Dynamic AMI Resolution**: Leveraged SSM Parameter Store integration to dynamically retrieve latest AMI IDs at launch time, ensuring multi-region compatibility without code alterations.
3. **Automated Server Bootstrapping**: Implemented automated software provisioning via `UserData`, deploying a fully configured Apache web server without requiring interactive SSH logins.
4. **Lifecycle and Drift Management**: Monitored deployment milestones via Stack Events and validated configuration consistency through native Drift Detection.
5. **Cloud Financial Governance (FinOps)**: Utilized CloudFormation automated dependency deletion to cleanly tear down all provisioned resources in a single API call, preserving 0 USD Free Tier compliance.

---

### FinOps Teardown Procedure:

To maintain zero cloud expenditure and adhere to the AWS Free Tier baseline, all resources provisioned in Week 7 are dismantled via CloudFormation reverse dependency deletion:

#### 1. Delete CloudFormation Stack:
Deleting the parent stack automatically terminates the EC2 instance `huylam-cfn-webserver` and removes the associated Security Group `WebServerSecurityGroup` without manual intervention:
```bash
aws cloudformation delete-stack --stack-name huylam-cfn-stack
```

#### 2. Monitor Teardown Completion:
Wait for stack deletion to complete via AWS CLI waiter:
```bash
aws cloudformation wait stack-delete-complete --stack-name huylam-cfn-stack
```

#### 3. Verify Clean Infrastructure State:
Validate that all compute and security group resources have been completely released:
```bash
# 1. Verify stack deletion
aws cloudformation describe-stacks --stack-name huylam-cfn-stack 2>&1 | grep "does not exist"

# 2. Verify EC2 instance termination
aws ec2 describe-instances --filters "Name=tag:aws:cloudformation:stack-name,Values=huylam-cfn-stack" \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name]" --output table

# 3. Verify Security Group removal
aws ec2 describe-security-groups --filters "Name=group-name,Values=*huylam-cfn-stack*" \
  --query "SecurityGroups[*].[GroupId,GroupName]" --output table
```
*Audit results confirm 100% of provisioned resources have been cleanly removed, maintaining zero cost overhead.*