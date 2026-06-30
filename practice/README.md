## Using Module

```bash
ansible -i inventory.ini servers -m ping
```

## Adhoc Commands

```bash
ansible -i inventory.ini servers -a "uptime"

ansible -i inventory.ini servers -a "df -h"
```

### Executing command as a `root user`

```bash
ansible -i inventory.ini servers -a "apt install nginx -y" --become
```

---

## Viewing Gathered Facts

All Facts

```bash
ansible -i inventory.ini servers -m setup
```

Specific Facts

```bash
ansible -i inventory.ini servers -m setup | grep "distribution"
```

---
## Playbooks

Playbook Syntax Check

```bash
ansible-playbook ./nginx/nginx-playbook.yml --syntax-check   # Without Inventory

ansible-playbook -i inventory.ini ./nginx/nginx-playbook.yml --syntax-check  # With Inventory
```


Playbook run command
```bash
ansible-playbook -i inventory.yml ./nginx/nginx-playbook.yml
```

---

## Ansible Vault

Encrypting secrets in `./vault/secrets.yml` file using the vault-password-file `./vault/vault_password.txt`

```bash
ansible-vault encrypt ./vault/secrets.yml --vault-password-file ./vault/vault_password.txt
```

Decrypting

```bash
ansible-vault decrypt ./vault/secrets.yml --vault-password-file ./vault/vault_password.txt
```

### Run the playbook

The below command will throw error when run after encrypting the password

```bash
ansible-playbook -i inventory.ini ./vault/secrets-playbook.yml
```

Correct Command

```bash
ansible-playbook -i inventory.ini ./vault/secrets-playbook.yml --vault-password-file ./vault/vault_password.txt
```
