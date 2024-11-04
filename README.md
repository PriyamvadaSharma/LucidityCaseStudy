# LucidityCaseStudy
Case study to monitor disk utilization for EC2 instances

There are two potential strategies for this solution. 
The first involves executing an Ansible playbook at the AWS Organization level, which would then be applied directly to the virtual machine instances.
**Approach 1 - Ansible playbook at the AWS Organization level**
1. Centralizing Access and Management Across AWS Accounts
* AWS Organizations and IAM Roles: Use AWS Organizations to centralize access. Set up cross-account IAM roles in each AWS account, granting a role in the management account permission to access EC2 instances in the other accounts. This role should have read-only access to EC2 and S3 to collect metrics and store outputs.
* Ansible Playbook Execution from Management Account: Run the Ansible playbooks in the management account using the cross-account role to SSH/RDP into instances and gather disk utilization metrics. Scripts in **DiskUtilization_Win.yml and DiskUtilization_Linux.yml** would be used in this case.

2. Aggregating Data from All Accounts

* S3 for Storage: Save collected disk utilization metrics as JSON files in a central S3 bucket accessible by the management account.
* AWS Lambda Aggregation: Configure an AWS Lambda function to periodically aggregate data from the S3 bucket, creating a consolidated report file in CSV or JSON format.
* Visualization with QuickSight: Optionally, import the report into Amazon QuickSight for dashboarding and monitoring.

3. Scaling for Future Accounts

* Automate Role Creation with CloudFormation: Use AWS CloudFormation StackSets to automate the creation of IAM roles in newly added accounts, streamlining integration.
* Dynamic Inventory in Ansible: Implement a dynamic inventory for Ansible, automatically discovering EC2 instances across accounts without manual modifications to the playbook.

Components Involved in the Process
* AWS Organizations: Manages access and permission centralization across multiple AWS accounts.
* IAM Roles: Provides cross-account access for Ansible to read EC2 instance data in each account.
* Ansible: Automates the disk utilization data collection.
* S3 Bucket: Stores the JSON output files with disk utilization data for easy aggregation.
* AWS Lambda Function: Aggregates data from S3 and compiles it into a report.
* AWS QuickSight: Optional visualization tool for data reporting and dashboarding, providing an interface for ongoing monitoring.
  
**Approach 2 - Ansible playbook for Installing Cloudwatch Agent**
The second strategy utilizes Ansible to deploy the CloudWatch agent across all EC2 instances as specified in the inventory.ini file. This setup allows us to monitor selected metrics, such as disk utilization, enabling visualization and alerting and sending to relevant team members accordingly or centralize it in S3 bucket.

1. Creating Inventory.ini file. with the IP addresses of our EC2 instances. The Inventory.ini file serves as a directory of hosts where Ansible tasks will be executed.
2. As part of this process, we’ll also require the **config.Json file** for configuring the CloudWatch Agent. This JSON file is a CloudWatch Agent configuration file that is used to collect and monitor system metrics, specifically for disk and memory usage, on an EC2 instance in AWS. The configuration file defines what metrics to collect, how often to collect them, and additional dimensions that provide context for the data. The configuration is designed to send data directly to CloudWatch, where it is stored in the CWAgent namespace. This approach allows for centralized monitoring, alerting, and visualization of metrics across instances directly within the AWS Management Console. Additionally, CloudWatch Logs can be accessed both in the CloudWatch console and directly in S3, under the specified bucket and prefix.
Hence, with config file - 
The CloudWatch Agent will send metrics to CloudWatch **for visualization and alerting purpose.**
Metrics will also be logged to an S3 bucket for backup or further analysis **for central place in digestable format**.
CloudWatch Logs can be accessed both in the CloudWatch console and directly in S3, under the specified bucket and prefix.   

3. MAKING PLAYBOOK FOR INSTALLING CWAGENT - We’re going to create a playbook that will effortlessly install the CloudWatch Agent on our EC2 instances using **install_cloudwatch_agent.yml** .This setup enables CloudWatch to monitor metrics on the EC2 instance based on the settings in the configuration file.
This playbook:
Downloads and installs the Amazon CloudWatch Agent.
Copies a configuration file to the target instance.
Moves the configuration file to the appropriate directory.
Starts the CloudWatch Agent with the specified configuration.

4. For future scaling purpose, Servers could be added dynamically for this process. This can be done in 2 ways -
* Dynamic Inventory Scripts: Instead of manually adding servers to a static inventory file, use dynamic inventory scripts. These scripts automatically fetch up-to-date server lists from cloud providers   like AWS, Azure, or Google Cloud.
* Cloud Inventory Plugins: Ansible includes built-in inventory plugins for major cloud providers (e.g., aws_ec2, azure_rm) that can query instances directly from your cloud account.

Components Involved in the Process
* Ansible Control Node: The main server where Ansible playbooks are executed.
* Playbooks: YAML files defining tasks for installing and configuring the CloudWatch Agent on target EC2 instances.
* AWS IAM Roles: Roles with permissions for instances to access CloudWatch and S3.
* CloudWatch Agent Configuration File: JSON file specifying metrics (e.g., CPU, memory) and log collection settings.
* EC2 Instances: Target instances across which the CloudWatch Agent is deployed.
* Amazon CloudWatch: AWS service where metrics are sent for monitoring.
* Amazon S3: Optional storage for CloudWatch logs or metrics backups.

