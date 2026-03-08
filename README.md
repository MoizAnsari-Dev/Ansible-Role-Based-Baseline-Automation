# Ansible Role-Based Baseline Server Automation

![img](https://ansible-dims-playbooks.readthedocs.io/en/latest/_images/ansible-overview.png?utm_source=chatgpt.com)

---

## 1. Introduction
This project automates the baseline configuration of Linux servers (Debian and RedHat based) using Ansible. It ensures that standard packages are installed, specific users are created, necessary directories are structured, and the SSH service is appropriately managed. 
Recently, it has been enhanced to support **AWS EC2 Dynamic Inventory**, automatically fetching running EC2 instances to configure.

---
## Project Objectives

- Dynamic Inventory from AWS EC2

- Install packages on Debian and RedHat based systems

- Create users

- Create directories

- Reload SSH service

- Standardize Linux server baselines

- Eliminate repetitive manual configuration

- Support Debian & RedHat-based systems

- Provide reusable, scalable automation

- Follow role-based Ansible architecture
---
## Key Features

- Dynamic Inventory from AWS EC2

- Role-based project structure

- Agentless SSH-based execution

- OS-aware automation using Ansible facts

- Bulk operations using loops

- Event-driven handlers

- Selective execution with tags

- Idempotent and production-safe
---
## High-Level Architecture
```
                ┌──────────────────────────┐
                │     Control Node         │
                │  (Ansible Installed)     │
                │                          │
                │  ├── Inventory           │
                │  ├── site.yml            │
                │  └── roles/              │
                │       └── baseline/      │
                │            ├── tasks     │
                │            ├── vars      │
                │            ├── handlers  │
                │            └── meta      │
                └───────────┬──────────────┘
                            │  SSH (Agentless)
            ────────────────┼─────────────────
                            │
        ┌───────────────────┴─────────────────┐
        │                                     │
┌───────────────┐                   ┌───────────────┐
│ Managed Node  │                   │ Managed Node  │
│   Server 1    │                   │   Server 2    │
│               │                   │               │
│ - Packages    │                   │ - Packages    │
│ - Users       │                   │ - Users       │
│ - Directories │                   │ - Directories │
└───────────────┘                   └───────────────┘

```
---
## 2. Prerequisites
Before running this automation, ensure the control node (your local machine or a dedicated Ansible server) has the following configured:

### Installed Software
- **Ansible**: `sudo apt install ansible` (or via `pip install ansible`)
- **Python 3 & Pip**: Required for executing Python-based AWS modules.
- **Boto3 & Botocore**: The AWS SDK for Python, required for Dynamic Inventory.
  ```bash
  pip install boto3 botocore
  ```

### AWS Credentials
Since this project uses AWS Dynamic Inventory, you must have your AWS credentials configured securely or passed via environment variables. By default, it uses `local_path/your_key_name.pem` as specified in `ansible.cfg`.

Make sure your AWS SSH Key has the correct permissions:
```bash
chmod 400 local_path/your_key_name.pem
```

## 3. Project Structure
Understanding the directory structure helps in customizing the roles.
```text
.
├── ansible.cfg                 # Main Ansible configuration file
├── Inventory/
│   └── aws_ec2.yml             # AWS Dynamic Inventory plugin configuration
├── roles/
│   └── baseline/
│       ├── tasks/main.yml      # Tasks to install packages, create users, and directories
│       ├── handlers/main.yml   # Event-based tasks (e.g., restart SSH daemon)
│       └── vars/main.yml       # Defined variables (packages, users, directories)
├── site.yml                    # The master playbook
└── README.md
```

## 4. Configuration

### `ansible.cfg`
The global configuration file adjusts the execution environment:
- **Inventory Location**: Automatically uses the AWS Dynamic Inventory script `Inventory/aws_ec2.yml`.
- **Remote User**: Connects using `ubuntu` by default.
- **Privilege Escalation**: `become = True` ensures all tasks are executed with root privileges (`sudo`).
- **Private Key**: Configured to `local_path/your_key_name.pem`.

### AWS Dynamic Inventory (`Inventory/aws_ec2.yml`)
Fetches instances dynamically from AWS:
- **Filtered by Tags**: Automatically targets instances with the tag `Env: web`.
- **AWS Prefix**: Variables fetched from AWS are prefixed with `aws_` to prevent collisions with reserved Ansible keywords.

---

## 5. Execution Guide

You can run the entire baseline process or execute specific parts using **Tags**.

### Ping All Servers
To verify connectivity to all your dynamically fetched AWS instances:
```bash
ansible all -m ping
```

### Run Full Baseline Configuration
To apply all baseline tasks (packages, users, directories) sequentially:
```bash
ansible-playbook site.yml
```

### Selective Execution using Tags
You can execute fractional parts of the configuration if you only need a specific setup by using `--tags`.

1. **Only Install Packages:**
   ```bash
   ansible-playbook site.yml --tags packages
   ```

2. **Only Create Users:**
   ```bash
   ansible-playbook site.yml --tags users
   ```

3. **Only Setup Directories:**
   ```bash
   ansible-playbook site.yml --tags directories
   ```

---

## 6. Variables Customization
To change which packages get installed or which users get created, simply modify the variables file:
**File:** `roles/baseline/vars/main.yml`

```yaml
packages:
  - git
  - curl
  - vim
  - htop

users:
  - appuser
  - devuser

directories:
  - /opt/app
  - /opt/logs
```
*Note: The package module correctly resolves naming strategies across OS families (e.g., Ubuntu/Debian vs RHEL/CentOS).*

---

## 7. Troubleshooting

- **`Permission denied (publickey)`**: Ensure your `.pem` key file is located exactly at `local_path/your_key_name.pem` and its permissions are locked down (`chmod 400`).
- **`Boto3 is required` error**: Run `pip3 install boto3 botocore`.
- **No hosts matched**: Ensure your EC2 instances specifically have an AWS Target Tag `Env` set to `web` as that is the configured filter in `Inventory/aws_ec2.yml`.


<img width="914" height="442" alt="image" src="https://github.com/user-attachments/assets/fde9d880-0b03-43ea-9ab4-643fc2d0e35f" />
