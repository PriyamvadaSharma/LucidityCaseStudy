# LucidityCaseStudy
Case study to monitor disk utilization for EC2 instances

There are two potential strategies for this solution. The first involves executing an Ansible playbook at the AWS Organization level, which would then be applied directly to the virtual machine instances.
**Solution for AWS Using Ansible**
1. Centralizing Access and Management Across AWS Accounts
AWS Organizations and IAM Roles: Use AWS Organizations to centralize access. Set up cross-account IAM roles in each AWS account, granting a role in the management account permission to access EC2 instances in the other accounts. This role should have read-only access to EC2 and S3 to collect metrics and store outputs.

Ansible Playbook Execution from Management Account: Run the Ansible playbooks in the management account using the cross-account role to SSH/RDP into instances and gather disk utilization metrics.  **Refer AnsiblePlaybook.yml**

2. Aggregating Data from All Accounts
S3 for Storage: Save collected disk utilization metrics as JSON files in a central S3 bucket accessible by the management account.

AWS Lambda Aggregation: Configure an AWS Lambda function to periodically aggregate data from the S3 bucket, creating a consolidated report file in CSV or JSON format.

Visualization with QuickSight: Optionally, import the report into Amazon QuickSight for dashboarding and monitoring.

3. Scaling for Future Accounts
Automate Role Creation with CloudFormation: Use AWS CloudFormation StackSets to automate the creation of IAM roles in newly added accounts, streamlining integration.

Dynamic Inventory in Ansible: Implement a dynamic inventory for Ansible, automatically discovering EC2 instances across accounts without manual modifications to the playbook.
