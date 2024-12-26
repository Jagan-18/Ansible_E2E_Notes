# Ansible common 
- It is typically refers to a set of tasks, configurations, or resources that are shared across multiple roles, playbooks, or systems. 
- It's not a built-in or special command, but a concept commonly used to manage things that should be applied universally or across multiple environments.

Here’s what’s typically **common** in Ansible roles:

### 1. **Common Tasks**
   A **common role** often includes tasks that are universally needed across multiple systems or environments. These tasks may involve:
   - Installing basic packages (like `vim`, `curl`, `git`).
   - Ensuring basic system configurations, such as setting up a timezone, configuring NTP (Network Time Protocol), or configuring a hostname.
   - Managing common users and groups across systems.
   - Setting up system logging, firewall rules, or other essential system configurations.

   Example task in a common role:
   ```yaml
   # roles/common/tasks/main.yml
   ---
   - name: Ensure common utilities are installed
     package:
       name:
         - vim
         - curl
         - wget
       state: present
   ```

### 2. **Common Handlers**
   Handlers in Ansible are used to perform actions only when notified by other tasks (e.g., restarting a service after configuration changes). A **common handler** might be used for actions like:
   - Restarting a service (e.g., NTP, SSH).
   - Rebooting a system after making critical changes.

   Example handler in a common role:
   ```yaml
   # roles/common/handlers/main.yml
   ---
   - name: restart sshd
     service:
       name: sshd
       state: restarted
   ```

### 3. **Common Variables**
   A **common role** might include default variables that are shared across multiple roles or playbooks. For example, default settings for the system or configurations that are expected on all hosts.
   
   Example variables in a common role:
   ```yaml
   # roles/common/defaults/main.yml
   ---
   system_timezone: "UTC"
   ```

### 4. **Common Files or Templates**
   A **common role** might include configuration files or templates that should be distributed to all systems, such as:
   - `/etc/hostname` or `/etc/hosts` files.
   - System-wide configuration templates (like `/etc/sysctl.conf`).
   - Common scripts or files that need to be deployed.

   Example file deployment in a common role:
   ```yaml
   # roles/common/tasks/main.yml
   ---
   - name: Copy common motd file
     copy:
       src: motd
       dest: /etc/motd
   ```

   Or a template:
   ```yaml
   # roles/common/templates/sysctl.conf.j2
   net.ipv4.ip_forward = 1
   ```

### 5. **Common Security Configurations**
   Security configurations, such as setting up firewalls, user permissions, or auditing configurations, can be considered part of a **common role**. These would be applied across systems to ensure security standards are followed universally.

### Example of Common Role Structure
A common role can be structured like this:
```plaintext
roles/
  common/
    tasks/
      main.yml        # Common tasks (e.g., installing packages, setting hostname)
    handlers/
      main.yml        # Common handlers (e.g., restarting services)
    defaults/
      main.yml        # Default variables for the role
    files/
      motd            # A file to be copied across all systems
    templates/
      sysctl.conf.j2  # A template to be rendered and copied
```

### Use Cases for Common Roles:
1. **System Initialization**: Tasks like configuring users, setting up SSH, configuring the timezone, and installing essential packages.
2. **Cross-Environment Configuration**: A common role can be used to apply consistent settings across different environments (dev, staging, prod).
3. **Reusability**: Using common configurations in various roles ensures DRY (Don't Repeat Yourself) principles, making your playbooks and roles easier to maintain.

### Conclusion:
In Ansible, **"common"** in the context of roles refers to tasks, variables, files, and configurations that are applied universally or across multiple systems. It is used to manage shared resources and settings that should be consistent across your infrastructure. By organizing these shared elements into a "common" role, you can reduce duplication and maintain consistency across your configurations.