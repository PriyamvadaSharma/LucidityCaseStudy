# LucidityCaseStudy
Case study to monitor disk utilization for EC2 instances

There are two potential strategies for this solution. 
The first involves executing an Ansible playbook at the AWS Organization level, which would then be applied directly to the virtual machine instances.
**Approach 1 - Ansible playbook at the AWS Organization level**
1. Centralizing Access and Management Across AWS Accounts
AWS Organizations and IAM Roles: Use AWS Organizations to centralize access. Set up cross-account IAM roles in each AWS account, granting a role in the management account permission to access EC2 instances in the other accounts. This role should have read-only access to EC2 and S3 to collect metrics and store outputs.

Ansible Playbook Execution from Management Account: Run the Ansible playbooks in the management account using the cross-account role to SSH/RDP into instances and gather disk utilization metrics. Scripts in **DiskUtilization_Win.yml and DiskUtilization_Linux.yml** would be used in this case.

2. Aggregating Data from All Accounts
S3 for Storage: Save collected disk utilization metrics as JSON files in a central S3 bucket accessible by the management account.

AWS Lambda Aggregation: Configure an AWS Lambda function to periodically aggregate data from the S3 bucket, creating a consolidated report file in CSV or JSON format.

Visualization with QuickSight: Optionally, import the report into Amazon QuickSight for dashboarding and monitoring.

3. Scaling for Future Accounts
Automate Role Creation with CloudFormation: Use AWS CloudFormation StackSets to automate the creation of IAM roles in newly added accounts, streamlining integration.

Dynamic Inventory in Ansible: Implement a dynamic inventory for Ansible, automatically discovering EC2 instances across accounts without manual modifications to the playbook.

**Approach 1 - Ansible playbook at the AWS Organization level**
The second strategy utilizes Ansible to deploy the CloudWatch agent across all EC2 instances as specified in the inventory.ini file. This setup allows us to monitor selected metrics, such as disk utilization, enabling visualization and alerting and sending to relevant team members accordingly or centralize it in S3 bucket.
1. Creating Inventory.ini file. with the IP addresses of our EC2 instances. The Inventory.ini file serves as a directory of hosts where Ansible tasks will be executed.
2. As part of this process, we’ll also require the **config.Json file** for configuring the CloudWatch Agent. This JSON file is a CloudWatch Agent configuration file that is used to collect and monitor system metrics, specifically for disk and memory usage, on an EC2 instance in AWS. The configuration file defines what metrics to collect, how often to collect them, and additional dimensions that provide context for the data. The configuration is designed to send data directly to CloudWatch, where it is stored in the CWAgent namespace. This approach allows for centralized monitoring, alerting, and visualization of metrics across instances directly within the AWS Management Console.
3. MAKING PLAYBOOK FOR INSTALLING CWAGENT - We’re going to create a playbook that will effortlessly install the CloudWatch Agent on our EC2 instances using **install_cloudwatch_agent.yml** .This setup enables CloudWatch to monitor metrics on the EC2 instance based on the settings in the configuration file. This playbook:
Downloads and installs the Amazon CloudWatch Agent.
Copies a configuration file to the target instance.
Moves the configuration file to the appropriate directory.
Starts the CloudWatch Agent with the specified configuration.
   
