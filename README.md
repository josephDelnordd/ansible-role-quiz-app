# Ansible Role: Quiz Deploy

## Description

This is a complete and portable Ansible role for deploying the Node.js `quiz-ansible` application on Debian/Ubuntu and RedHat/Rocky Linux systems. The role handles all aspects of deployment including system updates, Node.js installation, Git cloning, npm dependency management, application building, and starting the application with serve.

## Features

- ✅ Multi-OS support (Debian/Ubuntu, RedHat/Rocky)
- ✅ Automatic system package updates
- ✅ Node.js installation and configuration
- ✅ Git repository cloning
- ✅ NPM dependency installation
- ✅ Application building with npm
- ✅ Serve installation and configuration
- ✅ Asynchronous application startup
- ✅ Health check verification
- ✅ Comprehensive logging and reporting

## Requirements

- Ansible >= 2.9
- Supported OS:
  - Ubuntu 18.04, 20.04, 22.04
  - Debian 10, 11
  - RedHat EL 8, 9
  - Rocky Linux 8, 9

## Role Variables

### Default Variables (`defaults/main.yml`)

```yaml
# Application settings
quiz_app_name: "Quiz Application"
quiz_app_home: "/opt/quiz-ansible"
quiz_app_port: 3000
quiz_app_user: "root"
quiz_app_repo: "https://gitlab.com/ftutorials-labs/quiz-ansible.git"
quiz_app_version: "main"

# Node.js settings
quiz_nodejs_version: 23

# Installation flags
quiz_install_serve: true
quiz_system_update: true
quiz_serve_global: true

# Service settings
quiz_serve_async_timeout: 3600
```

## Usage

### Basic Usage

```yaml
---
- name: Deploy quiz-ansible application
  hosts: all
  become: true

  roles:
    - ansible-role-quiz-deploy
```

### With Custom Variables

```yaml
---
- name: Deploy quiz-ansible application
  hosts: all
  become: true

  vars:
    quiz_app_home: "/var/www/quiz-ansible"
    quiz_app_port: 8080
    quiz_nodejs_version: 22
    quiz_system_update: false

  roles:
    - ansible-role-quiz-deploy
```

## Tags

The role supports the following tags:

- `system_update` - Update system packages
- `dependencies` - Install dependencies
- `nodejs` - Install Node.js
- `git_clone` - Clone git repository
- `npm_dependencies` - Install npm dependencies
- `app_build` - Build application
- `serve_install` - Install serve package
- `service_management` - Manage service
- `health_check` - Health check
- `summary` - Display summary

### Run specific tag

```bash
ansible-playbook -i inventory.ini playbook.yml --tags nodejs
```

## Example Playbook

### Simple Deployment

```yaml
---
- name: Deploy quiz-ansible
  hosts: webservers
  become: true

  roles:
    - ansible-role-quiz-deploy
```

### Advanced Deployment

```yaml
---
- name: Deploy quiz-ansible with custom settings
  hosts: webservers
  become: true

  pre_tasks:
    - name: Ensure user can sudo without password
      lineinfile:
        path: /etc/sudoers
        state: present
        regexp: "^%sudo"
        line: "%sudo ALL=(ALL) NOPASSWD: ALL"
        validate: "visudo -cf %s"

  roles:
    - ansible-role-quiz-deploy

  post_tasks:
    - name: Verify deployment
      uri:
        url: "http://localhost:3000"
        status_code: 200
      retries: 5
      delay: 2
```

## 🚀 Running the Role

```bash
cd /root/projet

# Nettoyer d'abord
ansible -i inventaire.ini all -m ansible.builtin.shell -a "pkill -9 serve || true" -b
ansible -i inventaire.ini all -m ansible.builtin.file -a "path=/opt/quiz-ansible state=absent" -b
sleep 2

# Vérifier la syntaxe
ansible-playbook -i inventaire.ini playbooks/ansible-role-quiz-deploy.yaml --syntax-check

# Lancer le déploiement
ansible-playbook -i inventaire.ini playbooks/ansible-role-quiz-deploy.yaml -v

# Avec un tag spécifique
ansible-playbook -i inventaire.ini playbooks/ansible-role-quiz-deploy.yaml --tags nodejs
```

## Verify the application is running:

```bash
# Vérifier les processus
ansible -i inventaire.ini all -m ansible.builtin.shell -a "pgrep -f 'serve -s' && echo 'OK' || echo 'FAILED'" -b

# Tester les URLs
curl http://localhost:3003
curl http://localhost:3004
curl http://localhost:3005
curl http://localhost:3006
```

## Ansible Galaxy

### create an account and get API token

- Go to [https://galaxy.ansible.com](https://galaxy.ansible.com)
- Create an account
- Generate an API token on your profile page Galaxy (Settings → API Tokens → Generate Token)

### Install galaxy client

```bash
# Install Ansible Galaxy client
pip install ansible-galaxy
# Verify installation
ansible-galaxy --version
# Check path to ansible-galaxy
which ansible-galaxy
```

### Publish the role

```bash
cd /root/projet/roles/ansible-role-quiz-deploy

# Vérifier la qualité du rôle
ansible-lint

# Initialiser git (si pas déjà fait)
git init
git add .
git commit -m "Initial commit: ansible-role-quiz-deploy"

# Publier sur Galaxy
ansible-galaxy role import --api-key [YOUR_API_KEY] [GITHUB_USERNAME] [REPO_NAME]

# Example:
ansible-galaxy role import --api-key 8c894bd1fe02a7dc466fc933f285513eda7d033b josephDelnordd quiz_app
```

### URL Galaxy

After publishing, your role will be available at:

```bash
https://galaxy.ansible.com/[GITHUB_USERNAME]/[REPO_NAME]
```

## Supported Operating Systems

- Debian/Ubuntu: 18.04, 20.04, 22.04
- Debian: 10, 11
- RedHat/Rocky: 8, 9

## License

MIT