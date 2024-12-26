### Vault Setup Steps on Linux:

1. **Change Directory to Vault Folder:**
   ```bash
   cd /path/to/vault
   ```

2. **Create a Directory Inside the Vault Folder:**
   ```bash
   mkdir group_vars
   ```

3. **Create an Encrypted Vault File Using Ansible Vault:**
   Create a new vault file named `web.yaml` inside the `group_vars` directory.
   ```bash
   ansible-vault create group_vars/web.yaml
   ```
   - When prompted, enter the **Vault password** (e.g., `admin123`):
     - New vault password: `admin123`
     - Confirm new vault password: `admin123`
     - Save and exit the editor (e.g., in `vi`, you would use `:wq!`).

4. **Add Your Encrypted Data in the Vault File:**
   Once the file is open, add any sensitive data. For example:
   ```yaml
   ansible_user: centos
   ansible_password: DevOps321
   ```
   After adding your data, save and exit the editor (e.g., `:wq!` for `vi`).

5. **Check the Encrypted Vault Content:**
   You can check the encrypted content of the `web.yaml` file using the `cat` command:
   ```bash
   cat group_vars/web.yaml
   ```

6. **Run the Ansible Playbook:**
   To run your playbook (`01-vault_playbook.yaml`), you need to provide the Vault password either interactively or through a password file.

   - To prompt for the password interactively:
     ```bash
     ansible-playbook 01-vault_playbook.yaml --ask-vault-pass
     ```

   - Alternatively, you can provide the password non-interactively using the `--vault-password-file` option:
     ```bash
     ansible-playbook 01-vault_playbook.yaml --vault-password-file /path/to/vault_password_file
     ```

---

### Notes:
- **`ansible-vault create`**: This command creates a new encrypted file. The password you set during this step is used to encrypt and decrypt the file later.
- **`cat group_vars/web.yaml`**: This will show the encrypted contents. You cannot see the original values unless you decrypt them using the Vault password.
- **Interactive Vault Password**: The `--ask-vault-pass` option will prompt you to enter the Vault password when running the playbook. If you want to avoid manual input, you can use the `--vault-password-file` option to specify a file that contains the password.

---

This should guide you through setting up and using Ansible Vault effectively on your Linux system. Let me know if you need any more assistance!