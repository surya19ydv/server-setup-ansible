# Ansible Server Setup

Automated web server setup using Ansible. This project installs and configures Nginx, deploys a custom HTML page, creates a user, manages file permissions, and opens the firewall for HTTP traffic.

---

## Project Structure

```text
.
├── inventory.ini
├── playbook.yml
├── index.html
├── .gitignore
└── README.md
```

---

## Features

* Install required packages:

  * Nginx
  * Git
  * Curl
* Start and enable Nginx service
* Deploy a custom `index.html`
* Set file permissions
* Create application directory
* Create a new Linux user
* Configure firewall to allow HTTP traffic

---

## Playbook Tasks

### 1. Install Packages

Installs the latest version of:

* nginx
* git
* curl

```yaml
- name: install nginx
  apt:
    name:
      - nginx
      - git
      - curl
    state: latest
    update_cache: yes
```

### 2. Start Nginx

Starts the Nginx service and enables it on boot.

```yaml
- name: start nginx
  service:
    name: nginx
    state: started
    enabled: yes
```

### 3. Copy HTML File

Copies `index.html` from the local system to the server.

```yaml
- name: copy file
  copy:
    src: index.html
    dest: /var/www/html/index.html
    force: yes
```

### 4. Set Permissions

Sets execute and read permissions on the deployed HTML file.

```yaml
- name: set permission
  file:
    path: /var/www/html/index.html
    mode: "0755"
```

### 5. Create Directory

Creates a new folder named `app` inside `/var/www/html`.

```yaml
- name: create folder
  file:
    path: /var/www/html/app
    state: directory
```

### 6. Create User

Creates a Linux user named `suryadev`.

```yaml
- name: create user
  user:
    name: suryadev
    state: present
```

### 7. Configure Firewall

Allows incoming traffic on port 80 using UFW.

```yaml
- name: firewall setup
  ufw:
    rule: allow
    port: 80
    proto: tcp
```

---

## Inventory File Example

```ini
[webserver]
<your-server-public-ip> ansible_user=ubuntu ansible_ssh_private_key_file=ansible-key.pem
```

Example:

```ini
[webserver]
15.207.xx.xx ansible_user=ubuntu ansible_ssh_private_key_file=ansible-key.pem
```

---

## Run Commands

### 1. Set Permission for PEM File (Local)

```bash
chmod 400 ansible-key.pem
```

### 2. Test Connection (Optional)

```bash
ansible -i inventory.ini webserver -m ping
```

### 3. Run Playbook

```bash
ansible-playbook -i inventory.ini playbook.yml
```

---

## Copy (SCP) Command for PEM Usage

If your PEM file is in another location, copy it to your project folder:

```bash
cp /path/to/your/ansible-key.pem .
```

Example:

```bash
cp ~/Downloads/ansible-key.pem .
```

---

### 🖥️ Controller Node (Local Machine)

* This is your local system where Ansible is installed.
* Responsible for running the playbook.
* Connects to remote servers using SSH.

### ☁️ Managed Node (EC2 Server)

* AWS EC2 instance acts as the target server.
* Receives instructions from the controller node.
* Executes tasks like installing Nginx, creating users, etc.

### 🔗 Connection Flow

Controller (Local) ➜ SSH ➜ EC2 (Managed Node)

---

##
