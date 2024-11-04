High-Level Architectural Diagram
Approach 1 
Below is an outline of the architecture:

* Ansible: Run Ansible playbooks on a schedule from the centralized management account.
* IAM Roles: Enable cross-account access using IAM roles with sts:AssumeRole.
* EC2 Instances: Ansible collects disk utilization from each EC2 instance in all accounts.
* S3 Bucket: Collect JSON output from Ansible runs for centralized storage.
* Lambda Aggregator: A Lambda function aggregates the data and generates a report.
* QuickSight (Optional): For more advanced reporting and visualization.

  ┌───────────────┐                 ┌───────────────┐
  │ Management    │                 │ Acquired      │
  │ Account       │                 │ Account       │
  │               │                 │               │
  │ Ansible       │       Access    │ IAM Roles     │
  │ Playbook      │  ───────────>   │               │
  │   S3 Bucket   │                 │ EC2 Instances │
  └───────────────┘                 └───────────────┘
           │                               │
           │                               │
           │      Centralized Collection   │
           └──────────────────────────────>┘
           │
     ┌─────▼─────┐
     │  Lambda   │
     │ Aggregator│
     └─────┬─────┘
           │
     ┌─────▼─────┐
     │ QuickSight│
     │ Dashboard │
     └───────────┘

Approach 2
Below is an outline of the architecture:

* Ansible Control Node: The main server where Ansible playbooks are executed.
* Playbooks: YAML files defining tasks for installing and configuring the CloudWatch Agent on target EC2 instances.
* AWS IAM Roles: Roles with permissions for instances to access CloudWatch and S3.
* CloudWatch Agent Configuration File: JSON file specifying metrics (e.g., CPU, memory) and log collection settings.
* EC2 Instances: Target instances across which the CloudWatch Agent is deployed.
* Amazon CloudWatch: AWS service where metrics are sent for monitoring.
* Amazon S3: Storage for CloudWatch logs or metrics backups.

[Architectural diagram for 2nd approach](./image.png)
