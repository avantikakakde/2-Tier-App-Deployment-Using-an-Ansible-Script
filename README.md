

# 🚀 2-Tier App Deployment Using a Single Ansible Script

## 📌 Project Overview
This project demonstrates the **deployment of a 2-tier application architecture** using **Ansible automation**.

The architecture consists of:
- **Application Server (App Tier)** – Hosts the web application and related services  
- **Database Server (DB Tier)** – Hosts the database service  

Both tiers are **provisioned and configured using a single Ansible playbook**, highlighting best practices in **Infrastructure Automation** and **Configuration Management**.

This project is ideal for **DevOps beginners and Cloud learners** who want hands-on experience with real-world automation scenarios.

---
  
## 📁 Project Structure

├── inventory.ini

├── 2-tire.yml

└── README.md

---

## 🚀 Features
- Automated setup of **App Tier** and **DB Tier** using Ansible
- Single playbook to manage **multiple host groups**
- Clear separation of application and database responsibilities
- **Idempotent** and repeatable execution
- Easy to scale and reproduce in different environments

---

## ⚙️ Prerequisites

Ensure the following before running the playbook:

- Ansible installed on the control node  
  ```bash
  sudo yum  install ansible -y 
  ```
- SSH access to App Server and DB Server

- Properly configured inventory.ini  

## 🧾 Inventory Configuration (inventory.ini)

```

[appserver]
172.31.15.195 ansible_user=ec2-user ansible_ssh_private_key_file=/home/ec2-user/ansible.pem
[dbserver]
172.31.3.101 ansible_user=ec2-user ansible_ssh_private_key_file=/home/ec2-user/ansible.pem

```

## Explanation
- Hosts are grouped into appservers and dbservers
- SSH authentication is done using a private key
- Private IPs are used for internal communication

## Main Playbook (2-tier.yml)

# App Server Configuration
```
---
# appserver
- name: installation of appserver
  hosts: appserver
  become: yes
  vars:
   packages:
    - nginx
    - php
    - php-fpm
   services:
    - nginx
    - php-fpm
   web_file_path: /usr/share/nginx/html/index.php
  tasks:
  # install nginx, php
  - name: install nginx, php, php-fpm
    ansible.builtin.dnf:
      name: "{{packages}}"
      state: present
  # start and enable nginx, php
  - name: start and enable nginx, php-fpm
    ansible.builtin.systemd_service:
      name: "{{item}}"
      state: started
      enabled: true
    loop: "{{services}}"
  # deployment of php page
  - name: deploy php on nginx
    ansible.builtin.copy:
      dest: "{{web_file_path}}"
      content: |
       <?php
       phpinfo();
       ?>

```

# DB Server Configuration

```
#dbserver
- name: installation on dbserver
  hosts: dbserver
  become: yes
  vars:
   db_pkg: mariadb105-server
   db_service: mariadb
   db_name: FCT
  tasks:
  # install mariadb
  - name: install mariadb
    ansible.builtin.dnf:
      name: "{{db_pkg}}"
      state: present
  # start mariadb
  - name: start and enable maraidb
    ansible.builtin.systemd_service:
      name: "{{db_service}}"
      state: started
      enabled: true
  # create databse
  - name: create a  mysql database
    ansible.builtin.shell: |
      mysql -u root -e "CREATE DATABASE IF NOT EXISTS {{ db_name }};"

```

## 🖥️ App Server Task Explanation

### Install Packages
Installs **Nginx**, **PHP**, and **PHP-FPM** using the `dnf` package manager.

---

### Start Services
Ensures all required services are **started** and **enabled at boot**, so they remain active after system restarts.

---

### Deploy PHP Application
Creates an `index.php` file that displays **PHP configuration details**, verifying that the application stack is working correctly.

---

## 🗄️ DB Server Task Explanation

### Install MariaDB
Installs the **MariaDB database server** package required for application data storage.

---

### Start DB Service
Ensures the **MariaDB service** is started and enabled to run automatically at system startup.

---

### Create Database
Creates the application database using the **MySQL CLI**, preparing the database for application use.

---

## ▶️ Execution Command

Run the playbook using the following command:

```bash
ansible-playbook -i inventory.ini 2-tier.yml
```

### 🔹 PHP Application Output

Open the application in a browser:

http://<APP_SERVER_PUBLIC_IP>/index.php
You should see the PHP Info page, confirming a successful deployment.


### 🖼️ Screenshots


Ansible Playbook Execution
![](./images/Screenshot%202025-12-18%20192036.png)


AWS EC2 Instances

![](./images/Screenshot%202025-12-18%20191918.png)


PHP Output
![](./images/Screenshot%202025-12-18%20191945.png)
 
 ![](./images/Screenshot%202025-12-18%20192047.png)



### 🏁 Conclusion

This project demonstrates a real-world 2-tier application deployment using Ansible on AWS.
It showcases automation, infrastructure management, and service orchestration following DevOps best practices.
 