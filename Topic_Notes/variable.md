# Ansible Variable:
It is a key-value pair that can be used to store values that can be reused across playbooks, roles, or tasks. Variables allow you to make your playbooks more flexible and dynamic, enabling you to customize behavior based on different environments, configurations, or inputs.

### Defining Variables
You can define variables in several places in Ansible:

1. **In the playbook:**
   You can define variables directly inside the playbook in a `vars` section.

   ```yaml
   - hosts: all
     vars:
       my_variable: "Hello, Ansible!"
     tasks:
       - name: Show variable value
         debug:
           msg: "{{ my_variable }}"
   ```

2. **In inventory files:**
   Variables can be defined in the inventory file for specific hosts or groups.

   ```ini
   [webservers]
   web1 ansible_host=192.168.1.10
   web2 ansible_host=192.168.1.11

   [webservers:vars]
   ansible_user=ubuntu
   ansible_ssh_private_key_file=/path/to/private_key
   ```

3. **In `vars` files:**
   You can create separate YAML files to define variables, and then include them in your playbooks using the `vars_files` directive.

   ```yaml
   # vars.yml
   my_variable: "Value from vars file"
   ```

   ```yaml
   - hosts: all
     vars_files:
       - vars.yml
     tasks:
       - name: Show variable value
         debug:
           msg: "{{ my_variable }}"
   ```

4. **As command-line variables:**
   You can pass variables when running the playbook with the `-e` option.

   ```bash
   ansible-playbook playbook.yml -e "my_variable=HelloWorld"
   ```

5. **In hostvars:**
   You can access the variables of other hosts by using the `hostvars` dictionary.

   ```yaml
   - hosts: web1
     tasks:
       - name: Access variable from web2
         debug:
           msg: "{{ hostvars['web2']['my_variable'] }}"
   ```

6. **Through facts:**
   Ansible also gathers system information, called **facts**, which can be accessed like variables.

   ```yaml
   - hosts: all
     tasks:
       - name: Show system's IP address
         debug:
           msg: "{{ ansible_facts['ansible_default_ipv4']['address'] }}"
   ```

### Using Variables
Once you've defined variables, you can reference them within your playbook or tasks using the Jinja2 templating syntax, `{{ variable_name }}`.

```yaml
- hosts: all
  vars:
    app_name: "MyApp"
  tasks:
    - name: Install the application
      apt:
        name: "{{ app_name }}"
        state: present
```

### Types of Variables
1. **Scalar variables:** These store simple values like strings, integers, or booleans.

   ```yaml
   my_string: "Hello"
   my_number: 42
   my_boolean: true
   ```

2. **List variables:** These store a collection of values.

   ```yaml
   my_list:
     - item1
     - item2
     - item3
   ```

3. **Dictionary variables:** These store key-value pairs, like a JSON object.

   ```yaml
   my_dict:
     key1: value1
     key2: value2
   ```

### Precedence of Variables
Ansible follows a specific order of precedence for variables, which determines which variable will be used if multiple variables are defined with the same name. The order, from lowest to highest precedence, is:

1. **Inventory variables**
2. **Playbook variables**
3. **Host variables**
4. **Group variables**
5. **Facts**
6. **Registered variables**
7. **Command-line variables (`-e`)**

### Examples of Using Variables

- **Conditionals:**
  
  ```yaml
  - hosts: all
    vars:
      is_production: true
    tasks:
      - name: Perform action only in production
        debug:
          msg: "This is a production environment!"
        when: is_production
  ```

- **Loops:**
  
  ```yaml
  - hosts: all
    vars:
      packages:
        - nginx
        - git
        - curl
    tasks:
      - name: Install packages
        apt:
          name: "{{ item }}"
          state: present
        loop: "{{ packages }}"
  ```

### Best Practices
- Use descriptive variable names to improve playbook readability.
- Group related variables together in separate files (e.g., `vars/` directory).
- Avoid hardcoding sensitive data directly in playbooks. Instead, use Ansible Vault for encryption.

Variables in Ansible make automation more powerful and flexible by enabling you to customize playbook execution dynamically.