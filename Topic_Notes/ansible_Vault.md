# Ansible Vault
- It is a feature in Ansible that allows you to encrypt sensitive data, such as passwords, API keys, or private information, so that it can be safely stored in source control or shared in an automated deployment process. 
- This ensures that your sensitive data is not exposed in plaintext, while still allowing it to be used within your playbooks or roles.

# Here’s a summary of how to work with Ansible Vault:

### Basic Operations

1. **Encrypt a file**  
   You can encrypt a file (e.g., a YAML file containing sensitive data) using the `ansible-vault encrypt` command.
   
   Example:
   ```bash
   ansible-vault encrypt secrets.yml
   ```
   This will prompt you to enter a password that will be used to encrypt the file. The file `secrets.yml` will be encrypted and stored in its original location with `.vault` suffix added or overwriting the original file.

2. **Decrypt a file**  
   To decrypt a file, use the `ansible-vault decrypt` command:
   
   Example:
   ```bash
   ansible-vault decrypt secrets.yml
   ```

3. **Edit an encrypted file**  
   You can open and edit an encrypted file directly with `ansible-vault edit`. This will automatically decrypt the file in memory, allow you to make changes, and then re-encrypt it when saved.
   
   Example:
   ```bash
   ansible-vault edit secrets.yml
   ```

4. **View an encrypted file**  
   If you simply want to view an encrypted file without editing it, use the `ansible-vault view` command:
   
   Example:
   ```bash
   ansible-vault view secrets.yml
   ```

5. **Rekey a vault file**  
   If you need to change the password for an encrypted file, you can use the `ansible-vault rekey` command. This is useful if you want to rotate the password for a vault.
   
   Example:
   ```bash
   ansible-vault rekey secrets.yml
   ```

### Using Vault in Playbooks

Once you have encrypted your sensitive data, you can reference it in Ansible playbooks. To do this, you will need to pass the vault password either at runtime or through a password file.

- **Running a playbook with a Vault password**  
  You can pass the vault password interactively at runtime with the `--ask-vault-pass` option:
  ```bash
  ansible-playbook playbook.yml --ask-vault-pass
  ```

  Alternatively, you can use a password file with the `--vault-password-file` option:
  ```bash
  ansible-playbook playbook.yml --vault-password-file ~/.ansible_vault_pass.txt
  ```

### Vault and Inventory Files

You can store encrypted variables in your inventory or group variable files.

- **Example of encrypted variable in `group_vars/`**:
  ```bash
  ansible-vault encrypt group_vars/webservers/secrets.yml
  ```

### Vault Password File

A vault password file contains the password used to encrypt and decrypt Vault-encrypted files. You can generate a simple password file or use Ansible's `ansible-vault` to create one.

1. Create the vault password file manually:
   ```bash
   echo 'your-vault-password' > ~/.ansible_vault_pass.txt
   ```

2. Ensure the vault password file has proper permissions:
   ```bash
   chmod 600 ~/.ansible_vault_pass.txt
   ```

### Vault Lookup Plugin

You can also use Ansible Vault in playbooks to dynamically fetch and decrypt data using the `vault` lookup plugin.

Example:
```yaml
---
- hosts: localhost
  tasks:
    - name: Get a secret value from an encrypted file
      debug:
        msg: "{{ lookup('ansible.builtin.vault', 'secrets.yml') }}"
```

### Best Practices

1. **Use a Password Manager**: Store your Vault password in a secure password manager. 
2. **Limit Access**: Restrict access to encrypted files and password files.
3. **Environment Variables**: Use environment variables to inject the Vault password securely.
4. **Rotate Vault Passwords**: Regularly change and rotate passwords to maintain security.

### Advanced Use: Encrypting Single Variables

You don’t have to encrypt an entire file. You can encrypt just a single variable, using the `ansible-vault` command.

Example:
```bash
ansible-vault encrypt_string 'supersecretpassword' --name 'my_secret_password'
```

This will output a YAML snippet you can paste directly into your playbooks:
```yaml
my_secret_password: !vault |
  $ANSIBLE_VAULT;1.1;AES256
  62346566326338646632383833666166353734376534613664386432306238653238376364633461
  35343335623136346333633636636334623134653337653631303464363461323564343631383936
  66646562623234656332303735376662653362656366333135346636333433663437633535663038
```

### Summary

Ansible Vault is a powerful tool for securing sensitive data within your infrastructure automation workflows. It ensures that your secrets are encrypted and only accessible by those who know the vault password. By following best practices, you can maintain the security of your systems while automating deployments at scale.

-------------------------------------------------
## 1. **Create an Ansible Vault File**
      - You can create an encrypted file with Ansible Vault. Here are a few methods:

##### a. **Encrypt an Existing File**
   If you have an existing file (e.g., `secrets.yml`), you can encrypt it using the `ansible-vault encrypt` command:

   ```bash
   ansible-vault encrypt secrets.yml
   ```

   You will be prompted to enter a password for the vault. This password will be required later when you use the encrypted file.

##### b. **Create a New Encrypted File**
   You can directly create a new encrypted file using the `ansible-vault create` command. This command will open an editor (such as `vi` or `nano`), where you can add content to your encrypted file.

   ```bash
   ansible-vault create secrets.yml
   ```

   - An editor will open, where you can add sensitive information, for example:
     ```yaml
     api_key: "my_secret_api_key"
     db_password: "supersecretpassword"
     ```

   - When you save and exit, the file will be encrypted using the password you provided.

##### c. **Encrypt a Single Variable (String)**
   You can encrypt just a single variable using the `ansible-vault encrypt_string` command:

   ```bash
   ansible-vault encrypt_string 'supersecretpassword' --name 'db_password'
   ```

   This command outputs an encrypted string that you can include directly in your playbook or variable file. Example output:
   ```yaml
   db_password: !vault |
     $ANSIBLE_VAULT;1.1;AES256
     61626234303365353134623537373833653637633464323938616632323035336263393233313632
     64663235356131376365353766363631393937636336323631353262393437346633353631353637
     36373331333863343336303730366238346564623438643537383662656266656563633536646534
   ```

#### 3. **Use the Vault in Your Playbook**

Once you have an encrypted file, you can use it in your playbook by referencing the variables stored in that encrypted file.

Example playbook (`playbook.yml`):
```yaml
---
- name: Example Playbook Using Ansible Vault
  hosts: localhost
  vars_files:
    - secrets.yml  # This is the encrypted file
  tasks:
    - name: Print db password
      debug:
        msg: "The db password is {{ db_password }}"
```

#### 4. **Run the Playbook with Vault Password**

To run the playbook with the encrypted file, you will need to provide the Vault password so Ansible can decrypt the file. You can provide the Vault password in one of the following ways:

##### a. **Prompt for Password**
   Use the `--ask-vault-pass` option to prompt for the Vault password interactively:

   ```bash
   ansible-playbook playbook.yml --ask-vault-pass
   ```

##### b. **Use a Vault Password File**
   If you don’t want to enter the password interactively each time, you can store the password in a file (e.g., `~/.ansible_vault_pass.txt`) and pass it to Ansible using the `--vault-password-file` option:

   ```bash
   ansible-playbook playbook.yml --vault-password-file ~/.ansible_vault_pass.txt
   ```

   The `~/.ansible_vault_pass.txt` file should contain only the password, and it should have restricted permissions (e.g., `chmod 600 ~/.ansible_vault_pass.txt`).

#### 5. **Edit or View an Encrypted Vault File**

- To **edit** an encrypted file, use the `ansible-vault edit` command:

  ```bash
  ansible-vault edit secrets.yml
  ```

  This opens the file in an editor, where you can make changes. The file will be re-encrypted when you save and exit.

- To **view** an encrypted file without editing it, use the `ansible-vault view` command:

  ```bash
  ansible-vault view secrets.yml
  ```

#### 6. **Rekey an Ansible Vault File**

To change the password used to encrypt a Vault file, you can rekey the file:

```bash
ansible-vault rekey secrets.yml
```

You will be prompted for the old password and then asked to enter the new password.

#### 7. **Example: Full Playbook Using Vault**

Here’s a full example of how to use Ansible Vault in a playbook.

1. **Create `secrets.yml`** (using `ansible-vault create secrets.yml`):
   ```yaml
   db_password: !vault |
     $ANSIBLE_VAULT;1.1;AES256
     61626234303365353134623537373833653637633464323938616632323035336263393233313632
     64663235356131376365353766363631393937636336323631353262393437346633353631353637
   ```

2. **Create `playbook.yml`**:
   ```yaml
   ---
   - name: Example Playbook Using Ansible Vault
     hosts: localhost
     vars_files:
       - secrets.yml
     tasks:
       - name: Show DB password
         debug:
           msg: "The database password is {{ db_password }}"
   ```

3. **Run the Playbook**:
   ```bash
   ansible-playbook playbook.yml --ask-vault-pass
   ```

### Additional Commands for Ansible Vault

- **Encrypt a file**:
  ```bash
  ansible-vault encrypt myfile.yml
  ```

- **Decrypt a file**:
  ```bash
  ansible-vault decrypt myfile.yml
  ```

- **View a file**:
  ```bash
  ansible-vault view myfile.yml
  ```

- **Edit a file**:
  ```bash
  ansible-vault edit myfile.yml
  ```

- **Rekey a file** (change the password):
  ```bash
  ansible-vault rekey myfile.yml
  ```

### Summary

To create an Ansible Vault, you typically:

1. Use `ansible-vault create` to create a new encrypted file or `ansible-vault encrypt` to encrypt an existing file.
2. Use the encrypted file in your playbooks by including it as a `vars_files` entry.
3. Provide the Vault password during runtime using `--ask-vault-pass` or `--vault-password-file`.
4. Optionally, use other commands to edit, view, or rekey the Vault file.

Ansible Vault is a powerful way to manage sensitive data securely in your automation workflows.