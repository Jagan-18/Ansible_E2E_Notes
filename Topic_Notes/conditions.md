# Ansible Conditions: 
It allow you to control the flow of execution by running tasks or roles only when certain conditions are met. These conditions can be based on facts, variables, or any logical evaluation. Here’s an overview of how you can implement conditions in Ansible.

### 1. **Using `when` Condition**
   The most common way to apply conditions in Ansible is by using the `when` keyword. The `when` condition is used to execute a task only when a certain expression evaluates to `true`.

   - **Syntax:**
     ```yaml
     - name: task_name
       command: some_command
       when: condition
     ```

   - **Example:**
     ```yaml
     - name: Install Apache on Ubuntu
       apt:
         name: apache2
         state: present
       when: ansible_facts['distribution'] == 'Ubuntu'
     ```
     In this example, the task will only run when the `ansible_facts['distribution']` is equal to 'Ubuntu'.

### 2. **Logical Operators in Conditions**
   You can combine multiple conditions using logical operators like `and`, `or`, and `not`.

   - **`and`:** All conditions must be true.
     ```yaml
     when: ansible_facts['distribution'] == 'Ubuntu' and ansible_facts['os_family'] == 'Debian'
     ```

   - **`or`:** At least one of the conditions must be true.
     ```yaml
     when: ansible_facts['distribution'] == 'Ubuntu' or ansible_facts['distribution'] == 'Debian'
     ```

   - **`not`:** Reverses the condition, making it true if the expression is false.
     ```yaml
     when: not ansible_facts['distribution'] == 'Windows'
     ```

### 3. **Checking Variables or Facts**
   Conditions can be based on variables or facts gathered from the system. You can check for variable values, existence of variables, or specific facts.

   - **Checking if a variable is defined:**
     ```yaml
     - name: Task that runs if a variable is defined
       debug:
         msg: "Variable is defined"
       when: my_variable is defined
     ```

   - **Checking if a variable is undefined:**
     ```yaml
     - name: Task that runs if a variable is undefined
       debug:
         msg: "Variable is not defined"
       when: my_variable is undefined
     ```

   - **Checking if a variable is equal to a value:**
     ```yaml
     - name: Task for checking variable equality
       debug:
         msg: "Variable matches"
       when: my_variable == 'expected_value'
     ```

### 4. **Using `failed_when` and `changed_when`**
   You can override when Ansible considers a task to be successful or failed using the `failed_when` and `changed_when` directives. These are useful for more advanced conditions.

   - **`failed_when`:** Used to define custom conditions for failure.
     ```yaml
     - name: Check if a file exists, and fail if not
       stat:
         path: /etc/some_file
       failed_when: ansible_facts['stat']['exists'] == false
     ```

   - **`changed_when`:** Used to define custom conditions for determining if a task has changed the system.
     ```yaml
     - name: Check if a file was modified
       stat:
         path: /etc/some_file
       changed_when: ansible_facts['stat']['mtime'] > 1609459200  # Jan 1, 2021
     ```

### 5. **Using `register` with `when`**
   You can register the result of a task into a variable and use that variable in subsequent conditions. This is especially useful for controlling flow based on the output of a task.

   - **Example:**
     ```yaml
     - name: Get current user
       command: whoami
       register: user_result

     - name: Check if the user is root
       debug:
         msg: "You are root!"
       when: user_result.stdout == "root"
     ```

   In this case, the task to display the message will run only if the output of the `whoami` command is "root."

### 6. **Using `ansible_facts`**
   You can use system facts (gathered automatically by Ansible or with the `setup` module) as conditions. Facts are useful for targeting tasks based on system information like the operating system, architecture, memory, and more.

   - **Example:**
     ```yaml
     - name: Check if the system is running on Ubuntu
       debug:
         msg: "This is an Ubuntu system."
       when: ansible_facts['distribution'] == 'Ubuntu'
     ```

### 7. **Using `inventory` Variables**
   Sometimes, the condition depends on the inventory variables, like group or host names. You can check if a specific host belongs to a group, or if a particular group variable is set.

   - **Example:**
     ```yaml
     - name: Install Apache only on web servers
       apt:
         name: apache2
         state: present
       when: "'web' in group_names"
     ```

### 8. **Using `with_items` or `loop` with Conditions**
   You can use conditions with loops to selectively execute tasks for specific items in a list.

   - **Example with `with_items`:**
     ```yaml
     - name: Install packages only if they are not already installed
       apt:
         name: "{{ item }}"
         state: present
       with_items:
         - apache2
         - nginx
       when: item != 'nginx'  # Skip nginx installation
     ```

   - **Example with `loop`:**
     ```yaml
     - name: Install packages only if not installed
       apt:
         name: "{{ item }}"
         state: present
       loop:
         - apache2
         - nginx
       when: ansible_facts['distribution'] == 'Ubuntu'
     ```

### 9. **Nested Conditions**
   You can combine multiple conditions for more complex logic.

   - **Example:**
     ```yaml
     - name: Check if a variable exists and a service is active
       debug:
         msg: "Both conditions are met!"
       when:
         - my_variable is defined
         - ansible_facts['services']['nginx']['state'] == 'running'
     ```

---

### Summary of Condition Syntax:
- `when: condition`: Execute a task based on a condition.
- Logical operators: `and`, `or`, `not` to combine multiple conditions.
- `failed_when` and `changed_when` to control task success or failure.
- Use `register` to store the result of a task and check it in subsequent tasks.
- Use facts, inventory variables, and `with_items` or `loop` to control task execution.

By using these conditional statements, you can control the flow of your Ansible playbooks, making them more dynamic and flexible based on different scenarios or environments.