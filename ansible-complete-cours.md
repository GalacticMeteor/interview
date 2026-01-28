# Complete Ansible Course

## Table of Contents
1. [Introduction](#introduction)  
2. [Configuration and Basic Concepts](#configuration-and-basic-concepts)  
3. [Ansible Inventory](#ansible-inventory)  
4. [Ansible Variables and Facts](#ansible-variables-and-facts)  
5. [Ansible Playbooks](#ansible-playbooks)  
6. [Ansible Modules and Plugins](#ansible-modules-and-plugins)  
7. [Ansible Handlers, Roles, and Collections](#ansible-handlers-roles-and-collections)  
8. [Ansible Galaxy & Role Creation](#ansible-galaxy--role-creation)  
9. [Common Ansible Commands](#common-ansible-commands)  
10. [Most Used `ansible.builtin` Modules](#most-used-ansiblebuiltin-modules)  
11. [Ansible Templates](#ansible-templates)  
12. [Best Practices](#best-practices)  

---

## Introduction

**Ansible** is an open-source automation tool for configuration management, application deployment, and orchestration.  

**Key Points:**
- **Agentless:** No need to install software on managed nodes; uses SSH.
- **Idempotent:** Running the same task multiple times produces the same result.
- **Declarative:** Define desired state rather than procedural steps.
- **Use Cases:**
  - Deploying applications across multiple servers
  - Automating server setup and configurations
  - Continuous deployment pipelines
  - Orchestrating complex IT workflows

**Core Concepts:**
- **Control Node:** Machine where Ansible runs.
- **Managed Nodes:** Target machines controlled by Ansible.
- **Inventory:** Defines the managed nodes.
- **Playbook:** YAML file defining automation tasks.
- **Module:** Reusable units of work (e.g., installing packages, copying files).

---

## Configuration and Basic Concepts

**1. Ansible Configuration Files**
- Main config: `/etc/ansible/ansible.cfg` or `~/.ansible.cfg`
- Key settings:
```ini
[defaults]
inventory = ./hosts
remote_user = ansible
host_key_checking = False
```

**2. YAML Syntax**
- Ansible playbooks are written in YAML.
- Basic YAML rules:
  - Use 2 spaces per indent
  - Lists: `- item1`
  - Key-value: `key: value`

**3. Lab Example**
```yaml
# sample.yml
- hosts: all
  tasks:
    - name: Ping the server
      ping:
```

---

## Ansible Inventory

**1. Inventory Files**
- **INI format:**
```ini
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
```
- **YAML format:**
```yaml
all:
  hosts:
    web1.example.com:
    web2.example.com:
    db1.example.com:
  children:
    webservers:
      hosts:
        web1.example.com:
        web2.example.com:
    dbservers:
      hosts:
        db1.example.com:
```

**2. Grouping & Hierarchy**
- Groups allow running tasks on specific sets of servers.
- Parent-child groups:
```ini
[production:children]
webservers
dbservers
```

**3. Dynamic Inventory**
- Scripts or cloud plugins (AWS, Azure) can dynamically provide hosts.
```bash
ansible-inventory -i aws_ec2.yml --list
```

**Use Case:** Separate environments (prod, staging, dev) for different playbooks.

---

## Ansible Variables and Facts

**1. Variables Types**
- **Defined in playbooks:** `vars:`  
- **Inventory variables:** `host_vars` / `group_vars`  
- **Registered variables:** Capture module output
- **Facts:** Automatically gathered by Ansible (`ansible_facts`)

**2. Variable Precedence**
- Extra vars > Playbook vars > Inventory vars > Role defaults

**3. Example**
```yaml
- hosts: webservers
  vars:
    http_port: 8080
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
    - debug:
        msg: "Nginx will run on port {{ http_port }}"
```

**4. Register Example**
```yaml
- name: Check disk space
  ansible.builtin.command: df -h
  register: disk_info

- debug:
    var: disk_info.stdout_lines
```

---

## Ansible Playbooks

**1. Playbook Structure**
```yaml
- hosts: <group>
  become: yes
  vars:
    key: value
  tasks:
    - name: Task name
      ansible.builtin.shell:
        cmd: echo Hello World
```

**2. Tasks**
- Common `ansible.builtin` modules: `apt`, `yum`, `copy`, `file`, `service`, `command`, `shell`, `user`

**3. Handlers**
```yaml
- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
  listen: "nginx restart"
```
- Triggered via `notify: "nginx restart"`

**4. Conditionals**
```yaml
- name: Install package only on Ubuntu
  ansible.builtin.apt:
    name: vim
    state: present
  when: ansible_facts['os_family'] == "Debian"
```

**5. Loops**
```yaml
- name: Install multiple packages
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - curl
    - vim
```

**6. Playbook Verification**
```bash
ansible-playbook site.yml --check
ansible-playbook site.yml --diff
```

---

## Most Used `ansible.builtin` Modules

**1. Package Management:**
- `ansible.builtin.apt` - Manage packages on Debian/Ubuntu
- `ansible.builtin.yum` - Manage packages on RHEL/CentOS

**2. Files & Templates:**
- `ansible.builtin.copy` - Copy files to remote hosts
- `ansible.builtin.template` - Deploy Jinja2 templates
- `ansible.builtin.file` - Manage file attributes (permissions, owner)

**3. Services:**
- `ansible.builtin.service` - Manage services (start, stop, restart, enable)

**4. Commands & Scripts:**
- `ansible.builtin.command` - Run a command on remote host
- `ansible.builtin.shell` - Run shell commands (with pipe, redirection)
- `ansible.builtin.script` - Upload and execute a local script

**5. User & Group Management:**
- `ansible.builtin.user` - Manage users
- `ansible.builtin.group` - Manage groups

**6. Debug & Control:**
- `ansible.builtin.debug` - Print debug messages
- `ansible.builtin.assert` - Assert a condition
- `ansible.builtin.fail` - Fail the playbook with message
- `ansible.builtin.include_role` - Include a role dynamically
- `ansible.builtin.include_tasks` - Include tasks dynamically

**Example:**
```yaml
- name: Install packages
  ansible.builtin.apt:
    name: nginx
    state: present

- name: Deploy template
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf

- name: Ensure service is running
  ansible.builtin.service:
    name: nginx
    state: started
```

---

## Ansible Templates

**1. Jinja2 Templates**
```jinja
# templates/nginx.conf.j2
server {
    listen {{ http_port }};
    server_name {{ ansible_fqdn }};
    root /var/www/html;
}
```

**2. Using Templates in Roles**
```yaml
- name: Deploy nginx config
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: "Restart nginx"
```

**3. Realistic Use Case:** Generate environment-specific configs dynamically.

---

## Best Practices

- Keep **roles modular** and reusable.
- Use **group_vars** and **host_vars** for cleaner variable management.
- Always use **handlers** for service restarts.
- Use **`--check`** and **`--diff`** for safe deployment.
- Use **idempotent modules** whenever possible.
- Use **Ansible Galaxy** for role reuse.
- Keep playbooks **readable and well-commented**.


