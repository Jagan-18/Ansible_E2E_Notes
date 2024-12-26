# Ansible dynamic inventory refers: 
- It to an inventory that is generated dynamically at runtime, instead of being statically defined in an ini or yaml file. 
- This is useful when managing environments that change frequently, like cloud instances, containers, or virtual machines, where the host list may not be known ahead of time and can change frequently.
- A dynamic inventory script or plugin can query an external system (such as AWS, Azure, GCP, VMware, etc.) to fetch the list of hosts and their associated metadata, and then produce an inventory for Ansible to use during playbook execution.

## Steps to Set Up Ansible Dynamic Inventory for AWS EC2
### 1. Install Required Dependencies

First, make sure you have the required dependencies installed. The plugin requires boto3 and botocore libraries, which are used to interact with AWS:

bash
Copy code
pip install boto3 botocore

### 2. Set Up AWS Credentials

Ansible uses the AWS SDK (via boto3), which relies on AWS credentials to access resources. You can set up credentials in various ways:

- **Using AWS CLI:** Run aws configure to configure your credentials.
- **Environment Variables:** Set AWS credentials in the environment:

export AWS_ACCESS_KEY_ID=your-access-key-id
export AWS_SECRET_ACCESS_KEY=your-secret-access-key
export AWS_DEFAULT_REGION=us-east-1  # or your desired region

- **Using a shared credentials file (~/.aws/credentials):**


[default]
aws_access_key_id = your-access-key-id
aws_secret_access_key = your-secret-access-key
region = us-east-1  # default region

### 3. Configure Dynamic Inventory with the AWS EC2 Plugin

In your Ansible project directory, create a new directory for the inventory and configuration files, for example, inventory/aws_ec2.

- **Create an aws_ec2.yml configuration file:**

Create a file called aws_ec2.yml to define the settings for the AWS EC2 inventory plugin. Here's a basic example:

yaml
Copy code
plugin: aws_ec2
regions:
  - us-east-1  # Specify the AWS region(s) to query
filters:
  instance-state-name: running  # Only include instances that are running
group_by:
  - tags.Name  # Group instances by the value of the "Name" tag
keyed_groups:
  - key: tags.Environment
    prefix: env_  # Create groups based on the "Environment" tag

- **plugin:** This specifies the plugin to use, in this case, aws_ec2.
- **regions:** List the regions where your EC2 instances are running.
- **filters:** Filters used to narrow down the EC2 instances returned, such as filtering by instance state (running, stopped, etc.).
- **group_by:**  Groups instances based on their tags (e.g., grouping by the "Name" tag).
- ** keyed_groups:**  Defines additional grouping based on instance tags, such as "Environment" tags to group instances into env_production, env_staging, etc.

### 4.Run the Dynamic Inventory

After setting up the inventory configuration, you can use the aws_ec2 plugin in your playbook runs by specifying the path to the configuration file.

For example:

ansible-playbook -i inventory/aws_ec2/aws_ec2.yml playbook.yml
This will dynamically query your AWS EC2 instances and use them as inventory for your playbook.

### 5. Optional: Use Ansible Configuration File (ansible.cfg)

You can also configure Ansible to always use a particular dynamic inventory file by specifying it in the ansible.cfg file. Create an ansible.cfg file in your project directory:

[defaults]
inventory = ./inventory/aws_ec2/aws_ec2.yml
With this configuration, you don’t need to explicitly specify the inventory file in each command, as it will be automatically used.

#### Example: Advanced Configuration with Additional Features
You can also use more advanced configurations, such as selecting multiple AWS regions, including additional filters, and configuring more sophisticated groupings.

#### Example Configuration File: aws_ec2.yml
yaml
Copy code
plugin: aws_ec2
regions:
  - us-east-1
  - us-west-2
filters:
  instance-state-name: running
  tag: "Environment": "production"  # Only instances with this tag will be included
hostnames:
  - dns-name  # Use the instance's DNS name as the hostname
group_by:
  - tags.Name
keyed_groups:
  - key: tags.Application
    prefix: app_
  - key: tags.Environment
    prefix: env_

 - **regions:** Specifies multiple regions (us-east-1, us-west-2).
- **filters:** Filters instances by tag (e.g., only instances with Environment=production will be included).
- **hostnames:** Specifies that the instance’s DNS name should be used as the host name for inventory.
- **group_by and keyed_groups:** Groups instances by tags like Name, Application, and Environment.

**Testing the Inventory:**
You can test the dynamic inventory by running the ansible-inventory command:


ansible-inventory -i inventory/aws_ec2/aws_ec2.yml --list
This command will show you the dynamic inventory generated from AWS EC2. It will display the groups and hosts that have been detected based on your configuration file.

#### Example Output
json
Copy code
{
    "all": {
        "children": [
            "ungrouped",
            "env_production"
        ]
    },
    "env_production": {
        "hosts": [
            "i-1234567890abcdef0",
            "i-0987654321abcdef0"
        ],
        "vars": {
            "ansible_ssh_user": "ec2-user"
        }
    },
    "ungrouped": {
        "hosts": []
    }
}
# Conclusion
Using the AWS EC2 dynamic inventory plugin in Ansible is a powerful way to manage EC2 instances dynamically. You can filter instances based on tags, instance states, regions, and more, and group them by tags for easy management. By leveraging this plugin, you can avoid maintaining static inventory files and automate the management of your AWS infrastructure.



