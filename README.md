<p align="center">
    <!-- <img src="https://skillicons.dev/icons?i=ansible,gitlab,jenkins,harbor" /> -->
    <img src="https://raw.githubusercontent.com/yoonhyunchan/yoonhyunchan/refs/heads/main/logos/ansible-auto.svg" width="46"/>
    <img src="https://raw.githubusercontent.com/yoonhyunchan/yoonhyunchan/refs/heads/main/logos/gitlab-auto.svg" width="46"/>
    <img src="https://raw.githubusercontent.com/yoonhyunchan/yoonhyunchan/refs/heads/main/logos/jenkins-auto.svg" width="46"/>
    <img src="https://raw.githubusercontent.com/yoonhyunchan/yoonhyunchan/refs/heads/main/logos/harbor-auto.svg" width="46"/>
</p>

# CI/CD Infrastructure with Ansible

This Ansible playbook automates the deployment and configuration of a complete CI/CD infrastructure including GitLab, Jenkins, Harbor container registry, and web services with SSL certificate management.


## 📖 Blog Post
**[[Devops] 01. Gitlab Installation
](https://mon-roman.tistory.com/entry/01-Gitlab-Installation)** - My Blog Post<br>
**[[Devops] 02. Jenkins Installation
](https://mon-roman.tistory.com/entry/Devops-01-Jenkins-Installation)** - My Blog Post<br>
**[[Devops] 03. Setting Up Jenkins Agents
](https://mon-roman.tistory.com/entry/Devops-02-Setting-Up-Jenkins-Agents)** - My Blog Post<br>
**[[Devops] 04. Harbor Installation
](https://mon-roman.tistory.com/entry/Devops-01-Harbor-Installation)** - My Blog Post<br>

## 🚪 Next Step
**[[Devops] 05. Gitlab&Jenkins&Harbor Integration (1)
](https://mon-roman.tistory.com/entry/Devops-04-GitlabJenkinsHarbor-Integration-1)** - My Blog Post<br>
**[[Devops] 06. Gitlab&Jenkins&Harbor Integration (2)
](https://mon-roman.tistory.com/entry/Devops-06-GitlabJenkinsHarbor-Integration-2)** - My Blog Post<br>
**[[Devops] 07. Gitlab&Jenkins&Harbor Integration (3)
](https://mon-roman.tistory.com/entry/Devops-07-GitlabJenkinsHarbor-Integration-3)** - My Blog Post<br>

## ⚙️ Infrastructure as Code
**[Terraform Repository](https://github.com/yoonhyunchan/aws-terraform-project)** - My Repo

 Infrastructure setup for AWS EC2 instances, networking, and security groups required for the Kubernetes cluster deployment.

## 🏗️ Architecture Overview


<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2Fbd1oJy%2FbtsOMOP7ON2%2FAAAAAAAAAAAAAAAAAAAAAAwpDOE_7G42o9KU3SgbD1RbRItI8Asg9ymLsDMPFGG3%2Fimg.png%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1756652399%26allow_ip%3D%26allow_referer%3D%26signature%3DVfQn2iIxuQcwrleGRUw%252Fp8mXQ%252B8%253D"  />



The infrastructure consists of the following components:

- **GitLab CE** - Source code management and CI/CD
- **Jenkins Controller** - CI/CD automation server
- **Jenkins Agents** - Build executors (2 agents)
- **Harbor** - Container registry
- **Web Server**(Option) - Nginx reverse proxy with SSL
- **Certificate Authority** - Custom CA for internal SSL certificates

## 📋 Prerequisites

### System Requirements
- Amazon Linux 2023 or compatible RHEL-based systems
- Ansible 2.9+
- SSH access to all target servers
- Terraform-generated SSH key

### Required Collections
```bash
ansible-galaxy collection install -r requirements.yml
```

## 🚀 Quick Start

1. **Clone the repository**
   ```bash
   git clone https://github.com/yoonhyunchan/cicd-ansible
   cd cicd-ansible
   ```

2. **Update inventory**
   Edit `inventory.ini` with your server hostnames and ensure SSH key path is correct.

3. **Update variables**
   Modify `vars.yml` with your domain names:
   ```yaml
   external_domain: "your-domain.com"
   internal_domain: "your-internal-domain.com"
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory.ini site.yml
   ```

## 📁 Project Structure

```
cicd-ansible/
├── ansible.cfg              # Ansible configuration
├── inventory.ini            # Host inventory
├── site.yml                 # Main playbook
├── vars.yml                 # Global variables
├── requirements.yml         # Ansible collections
└── roles/
    ├── ca_root/            # Certificate Authority setup
    ├── ca_signed/          # SSL certificate generation
    ├── setup_init/         # Initial system setup
    ├── gitlab/             # GitLab CE installation
    ├── jenkins/            # Jenkins controller setup
    ├── agent/              # Jenkins agent configuration
    ├── harbor/             # Harbor container registry
    └── web/                # Nginx web server
```

## 🔧 Components

### Certificate Authority (CA)
- Generates a custom Certificate Authority
- Creates SSL certificates for all internal services
- Distributes CA certificates to all hosts

### GitLab CE
- Installs GitLab Community Edition
- Configures with custom SSL certificates

### Jenkins
- Installs Jenkins with Amazon Corretto 21
- Configures HTTPS with custom SSL certificates
- Sets up SSH connectivity to agents

### Jenkins Agents
- Configures build executors
- Sets up SSH connectivity from controller
- Prepares environment for builds

### Harbor Container Registry
- Installs Docker and Docker Compose
- Deploys Harbor v2.12.3
- Configures with custom SSL certificates
- Sets up Docker certificate trust

### Web Server (Option)
- Installs and configures Nginx
- Sets up reverse proxy with SSL
- Uses Let's Encrypt certificates for external domain
- It is test web server for me
 

## 🔐 Security Features

- Custom Certificate Authority for internal services
- SSL/TLS encryption for all services
- SSH key-based authentication
- Proper file permissions and ownership
- CA certificate distribution to all hosts

## 🌐 Network Configuration

### Internal Domain
- All services use `internal_domain` for internal communication
- Custom CA certificates for internal SSL


## 📝 Configuration Files

### Inventory Structure
```ini
[web]
bastion.example.internal

[gitlab]
gitlab.example.internal

[jenkins_controller]
jenkins.example.internal

[jenkins_agent]
jenkins_agent1.example.internal
jenkins_agent2.example.internal

[harbor]
harbor.example.internal

[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=/home/ec2-user/terraform-key.pem
```

### Variables
- `external_domain`: External domain for web access
- `internal_domain`: Internal domain for service communication