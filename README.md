# Ansible Role-Based Baseline Server Automation
![img](https://ansible-dims-playbooks.readthedocs.io/en/latest/_images/ansible-overview.png?utm_source=chatgpt.com)

---
## Overview

This project demonstrates a production-grade Ansible role used to standardize Linux server configurations across multiple hosts.
It automates bulk package installation, user creation, and directory provisioning using Ansible roles, loops, facts, handlers, and tags.

The project follows real DevOps best practices and is suitable for enterprise environments.

---
## Project Objectives

- Standardize Linux server baselines

- Eliminate repetitive manual configuration

- Support Debian & RedHat-based systems

- Provide reusable, scalable automation

- Follow role-based Ansible architecture
---
## Key Features

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
                ┌─────────────────────────┐
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
                └───────────┬─────────────┘
                            │  SSH (Agentless)
            ─────────────────┼─────────────────
                            │
        ┌───────────────────┴───────────────┐
        │                                   │
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
## Project Structure
```
ansible-bulk-role/
├── inventory
├── site.yml
└── roles/
    └── baseline/
        ├── tasks/
        │   └── main.yml
        ├── vars/
        │   └── main.yml
        ├── handlers/
        │   └── main.yml
        └── meta/
            └── main.yml
```
---
## Technologies Used

- Ansible

- YAML

- SSH

- Linux (Ubuntu / CentOS / RHEL)
---
## Inventory Example
```
[servers]
192.168.1.10
192.168.1.11

[all:vars]
ansible_user=ubuntu

```
---
## Role Variables

roles/baseline/vars/main.yml
```
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
---
## Role Tasks (Highlights)

- Install packages using OS-specific package managers

- Create multiple users using loops

- Create standard directory structure

- Reload services using handlers when required
---
## How to Run
### Run full automation
```
ansible-playbook -i inventory site.yml

```
### Run only package installation
```
ansible-playbook -i inventory site.yml --tags packages
```
### Run only user creation
```
ansible-playbook -i inventory site.yml --tags users

```
### Run only directory setup
```
ansible-playbook -i inventory site.yml --tags directories
```
---
## Verification
```
ansible servers -i inventory -m shell -a "git --version"
ansible servers -i inventory -m shell -a "id appuser"
ansible servers -i inventory -m shell -a "ls -ld /opt/app /opt/logs"
```
---
## Ansible Concepts Demonstrated
- Roles & reusable automation

- Inventory grouping

- Variables & vars_files

- Loops (loop, item)

- Facts (ansible_os_family)

- Handlers (notify)

- Tags for selective execution

- Idempotency

<img width="914" height="442" alt="image" src="https://github.com/user-attachments/assets/fde9d880-0b03-43ea-9ab4-643fc2d0e35f" />
