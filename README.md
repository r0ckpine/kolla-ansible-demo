# Kolla-ansible Deployment Demo

A demonstration project for deploying OpenStack using kolla-ansible.

[Kolla-ansible](https://docs.openstack.org/kolla-ansible/latest/index.html) deploys OpenStack services as Docker containers and manages them with Ansible.

## Supported Scenarios

This project supports two deployment scenarios:

1. **All-in-One**: Single host deployment with all OpenStack services
2. **Multi-node**: 3-node deployment with 1 controller and 2 compute nodes

## Requirements

### Operating System
- Rocky Linux 9
- CentOS Stream 9

### Network Configuration
Each VM requires 2 network interfaces:
- **eth0**: Management network
- **eth1**: External network (for Neutron)

## Getting Started

### Prerequisites
1. Install required packages:
   ```bash
   sudo dnf -y install ansible-core git
   ```
2. Clone this repository
3. Configure host variables (see Configuration section)

### Where to Run
Run this playbook on the target deployment node:
- **All-in-one**: Run on the all-in-one node
- **Multi-node**: Run on the controller node
  - Node hostnames must be `controller`, `compute1`, and `compute2`
  - All nodes must resolve each other's hostnames and allow SSH access
  - Ensure `/etc/hosts`, `.ssh/config`, and SSH keys are properly configured

## Quick Start

1. **Configure host variables:**
   ```bash
   vim inventory/host_vars/kolla.yml
   ```

2. **Deploy OpenStack:**
   ```bash
   ansible-playbook -i inventory site.yml
   ```

## Deployment Options

### Complete Deployment
```bash
ansible-playbook -i inventory site.yml
```

### Step-by-Step Deployment
```bash
# Install kolla-ansible
ansible-playbook -i inventory site.yml --tags install

# Configure kolla-ansible
ansible-playbook -i inventory site.yml --tags config

# Deploy OpenStack services
ansible-playbook -i inventory site.yml --tags deploy

# Post-deployment setup
ansible-playbook -i inventory site.yml --tags post
```

### Remove Deployment
```bash
ansible-playbook -i inventory kolla_destroy.yml
```

## Configuration

Key variables in `inventory/host_vars/kolla.yml`:
- `kolla_version`: Kolla-ansible git branch/tag (default: master)
- `kolla_venv`: Virtual environment path
- `kolla_inventory`: Deployment type (all-in-one or multinode)

## Deployment Process

The deployment consists of four stages:

1. **Install**: Sets up Python environment and installs kolla-ansible from git
2. **Configure**: Generates kolla passwords and applies configuration templates
3. **Deploy**: Runs kolla-ansible bootstrap, prechecks, and deployment
4. **Post-Deploy**: Additional configuration after OpenStack deployment

This creates a fully functional OpenStack environment for demonstration and testing.

## Using Your OpenStack Cloud

Once deployment completes, your OpenStack cloud is ready to use with example resources already configured.

### Web Interface
Access Horizon (OpenStack Dashboard) at: `http://<node-address>/`

### Command Line Interface

1. **Log in to the deployment node**

2. **Activate the virtual environment:**
   ```bash
   source kolla-venv/bin/activate
   ```

3. **Create your first instance:**
   ```bash
   openstack --os-cloud=kolla-admin server create \
     --image cirros \
     --flavor m1.tiny \
     --key-name mykey \
     --network demo-net \
     demo1
   ```

## Important Notes

⚠️ **Compatibility Warning**: The Kolla-ansible upstream repository changes frequently. This demo might not work with the latest versions. These playbooks are based on official Kolla-ansible guides with modifications for proper functionality.
