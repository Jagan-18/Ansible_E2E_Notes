# Ansible loops: 
It are a way to repeat a task multiple times with different values. Loops allow you to perform repetitive tasks efficiently, like configuring multiple servers, installing multiple packages, or managing several users.

### Types of Loops in Ansible

1. **Basic Loop** (`loop`)
2. **With_* Loops** (`with_items`, `with_dict`, etc.)
3. **Loop with index** (`loop` with `index_var`)

### 1. **Basic Loop**

This is the most common way to loop over a list or dictionary.

```yaml
- name: Install multiple packages
  ansible.builtin.yum:
    name: "{{ item }}"
    state: present
  loop:
    - httpd
    - vim
    - git
```

In this example, the `yum` module installs the `httpd`, `vim`, and `git` packages.

### 2. **With_* Loops**

Ansible has several predefined looping constructs, such as `with_items`, `with_dict`, `with_fileglob`, and more.

#### **`with_items`**

Used for looping over a list of items.

```yaml
- name: Create multiple users
  ansible.builtin.user:
    name: "{{ item }}"
    state: present
  with_items:
    - alice
    - bob
    - charlie
```

#### **`with_dict`**

Used for looping over key-value pairs in a dictionary.

```yaml
- name: Add users with their specific shell
  ansible.builtin.user:
    name: "{{ item.key }}"
    shell: "{{ item.value }}"
  with_dict:
    alice: /bin/bash
    bob: /bin/zsh
```

#### **`with_fileglob`**

Loops over files matching a glob pattern.

```yaml
- name: Remove old log files
  ansible.builtin.file:
    path: "{{ item }}"
    state: absent
  with_fileglob:
    - "/var/log/*.log"
```

### 3. **Loop with Index**

You can access the index of the current loop iteration using `loop.index` (1-based index) or `loop.index0` (0-based index).

```yaml
- name: Print each item with its index
  debug:
    msg: "Item {{ item }} is at index {{ loop.index }}"
  loop:
    - apple
    - banana
    - cherry
```

### 4. **Nested Loops**

You can also create nested loops, where each loop can be based on different variables or data structures.

```yaml
- name: Install packages for multiple environments
  ansible.builtin.yum:
    name: "{{ item.0 }}"
    state: present
  loop:
    - [ 'httpd', 'vim', 'git' ]
    - [ 'nginx', 'curl' ]
```

In this case, `loop` iterates over two sets of packages for different environments.

### 5. **Loop Control**

You can control the behavior of loops using several loop control options, such as `loop_control`, `until`, and `retries`.

#### **`loop_control`**

Used to customize the behavior of the loop. You can change things like how the loop variable is named, the starting index, and more.

```yaml
- name: Debug items with custom loop control
  debug:
    msg: "Item: {{ item }}"
  loop:
    - apple
    - banana
    - cherry
  loop_control:
    index_var: idx
    label: "{{ item }}"
```

### Common Loop Variables
- `loop.index` - 1-based index of the loop
- `loop.index0` - 0-based index of the loop
- `loop.first` - Boolean indicating if it's the first item
- `loop.last` - Boolean indicating if it's the last item
- `loop.length` - The length of the loop

### Example of Complex Loops

```yaml
- name: Example of looping with both item and index
  debug:
    msg: "Processing {{ item }} at index {{ loop.index }}"
  loop:
    - apple
    - banana
    - cherry
```

In this case, Ansible will print the current item and its index in the loop.

### Summary

- **`loop`** is the most general looping construct.
- **`with_*` loops** are legacy methods, though still widely used, especially for specific use cases like `with_items` and `with_dict`.
- **`loop_control`** provides customization for loop behavior.
