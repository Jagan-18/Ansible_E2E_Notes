
### Shell Script Disadvantages:
1. **Portability Issues**: Shell scripts may not work consistently across all Linux distributions due to differences in shell versions or available utilities.
2. **Scalability**: Shell scripts can be difficult to scale when managing a large number of servers or complex infrastructure setups.
3. **Error Handling and Validation**: Shell scripts lack advanced error handling and validation features, requiring manual implementation for effective error management.
4. **Readability Issues**: Shell scripts can become hard to read and maintain, especially for complex logic or when poorly documented.
5. **Limited Cross-Platform Compatibility**: Shell scripts are typically designed for Unix-like systems (e.g., Linux, macOS) and do not work seamlessly with other external systems or operating environments like Windows.


### Ansible:
1. Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code.
2. Ansible provides two ways for users to manage servers: through ad-hoc commands and playbooks. This article focuses on how to configure servers using playbooks.
3. Control Node as your node where ansible install.
4. Master Node as your target Node.

Ex: Its an Automation Configuration and management tool.
1. Configuration: Hardware & Software process.
2. Management: Update, Install, Uninstall…!


### Ansible features:
1. **Platform Independent**: Ansible works across different platforms, including Linux, Windows, and cloud environments.
2. **Scalable**: Ansible can easily scale from managing a few machines to thousands of systems.
3. **Idempotence**: Ansible ensures that repeated executions of the same playbook yield the same result without unintended changes.
4. **Error Handling**: Ansible provides built-in mechanisms for managing errors and controlling flow based on conditions.
5. **Readable and Maintainable**: Ansible playbooks are written in YAML, making them simple to read, write, and maintain.
6. **Vault**: Ansible Vault allows you to securely encrypt sensitive data, such as passwords and keys, within playbooks.
7. **Rich Modules**: Ansible includes a wide range of pre-built modules for managing various systems, applications, and services.
8. **Agentless**: Ansible doesn't require any agents or additional software on the target systems to function.


## As a DevOps engineer we need to do this effectively. Basically CRUD over the server.

#### 1. Creation of configuration --> should be fast and accurate
#### 2. Update the configuration --> any changes in configuration should be rolled out asap.
#### 3. Delete the configuration --> delete the configuration if not required.
#### 4. Read the configuration --> it is easy to read the configuration of server through ansible playbooks.


# PULL:

1. Nodes should connect to the servers through agent software.
2. Nodes should download the configuration periodically, say every 30min.
3. Even there is no change in configuration, agents connect to servers that creates unnecessary traffic and data consumption.
Ex: chef

# PUSH:

1. Unlike pull there is no need of agent installation.
2. Whenever there is a change in configuration, server can push to the nodes directly.
3. Ansible uses SSH authentication to connect to the servers.
Ex: Ansible



```bash
sshpass -p "DevOps321" ssh centos@<node_IPaddress>
```
### that Ansible is installed on your control machine. If it's not already installed, you can install it using the package manager of your system.
```bash
sudo yum install ansible -y 
```

### To connect an Ansible server (control machine) to a remote node (managed node) 

```bash
ansible -i <IP_address_Node>, all -e ansible_user=centos -e ansible_password=DevOps321 -m ping
```
### Explanation:
#### 1. -i <IP_address_Node>,: The -i flag defines the inventory, which in this case is a single node with the IP address <IP_address_Node>. The trailing comma is required to specify it as a single host and avoid Ansible interpreting it as a file.
#### 2. all: This means "run the task on all nodes specified in the inventory" (in this case, only one node is defined).
#### 3.-e ansible_user=centos: Specifies that the Ansible user for SSH is centos.
#### 4.-e ansible_password=DevOps321: Supplies the password DevOps321 for SSH authentication.
#### 5. -m ping: Uses the ping module to check the connection to the node. It sends a simple "pong" response if the connection is successful.


## To execute will run an Ansible ad-hoc task to install the nginx package on a node specified by its IP address. 

```bash
ansible -i <IP_address_Node>, all -e ansible_user=centos -e ansible_password=DevOps321 --become -m yum -a "name=nginx state=present"
```
<!--
Explanation of the Command:
==========================
1. ansible: The Ansible command-line tool for running tasks on managed hosts.
2. -i <IP_address_Node>,:
       - This option specifies the inventory of hosts.
       - You need to replace <IP_address_Node> with the actual IP address of your node (e.g., 192.168.1.10).
    -  The comma , after the IP address is necessary for a single host to be passed in a dynamic fashion. Without the comma, it would expect a file or a range of hosts.

3. all: 
    - Refers to the group of hosts in the inventory. Since you're targeting only one host (the one specified by the IP address), this still works, though it's typically used in larger inventories.

4. -e ansible_user=centos: This sets the SSH user to centos. This is the user used to log into the target machine.

5. -e ansible_password=DevOps321: This passes the password for the centos user.
 - 
 While this works, passing passwords in the command line is not secure and should be avoided in production environments. Consider using Ansible's ansible-vault to securely manage sensitive data like passwords.

6. --become: This flag tells Ansible to use privilege escalation (sudo) to execute the task as a superuser on the target machine. Without this, Ansible would attempt to run the command as the logged-in user, which may not have the required permissions to install packages.

7. -m yum: Specifies the yum module, which is used to manage packages on Red Hat-based systems (e.g., CentOS, RHEL). This will allow you to install, remove, or update packages.

8. -a "name=nginx state=present":
The arguments passed to the yum module:
        - name=nginx: Tells Ansible to work with the nginx package.
        - state=present: Ensures that nginx is installed. If it's already installed, no changes will be made; if it's not installed, it will be installed.

-->
# Ansible Playbook:
 ### 1. Ansible Playbook is a file that contains a set of tasks to be executed on one or more hosts. 
 ### 2. These tasks are defined in a declarative YAML format and can include operations such as installing packages, starting services, copying files, etc. 
 ### 3. Playbooks allow for powerful automation, ensuring consistency and repeatability in managing systems and applications across an infrastructure.

###  What is YAML?
#### YAML (YAML Ain't Markup Language) is a human-readable data serialization format that is commonly used for configuration files and data exchange between languages with different data structures.

Ex:
1. DTO - Data Transfer object.
2. XML - Externsive Markup Language.
   <User>
       <Email></Email>
       <Password></Password >
   </User>
3. Json  - Java Script Object Notation
      {
        "Email": "info@devops.com"
        "password": "admin123"      
      }
4. YAML Ain't Markup Language (key:value)

email: infor@devops.com

password: admi123

ex: yaml format
- name: jagadeesh
  dob: "1999-05-07"
  address: 
    address-line1: Btm stage.
    city: bengaluru
  email: infro@devops.com

### To run ansible playbook below command we will use. 
  ```bash
  ansible-playbook -i inventory -e ansible_user=centos -e ansible_password=DevOps321 <file_name>
  ```


## command module: 
- The command module is able to execute only the binaries on remote hosts. The command module won’t be impacted by local shell variables because it bypasses the shell. At the same time, it may not be able to run “shell” built-in features and redirections.

## shell module: 

- The shell Ansible module is potentially more dangerous than the command module and should only be used when you actually really need the shell functionality. So if you’re not stringing two commands together (using pipes or even just && or ;), you don’t really need the shell module. Similarly, expanding shell variables or file global requires the shell module. If you’re not using these features, don’t use the shell module. Sometimes it’s the only way, I know.


# what is ansble role's.?

















