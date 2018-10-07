# Instructions

## Overview

This tutorial will set up Prometheus with 2 HA replicas, Pushgateway, Alertmanager, Node Exporter, and Grafana, using [CloudAlchemy's Ansible roles](https://github.com/cloudalchemy/).

## Setup

### Provision VMs

Create 4 VMs on a cloud provider for the following purposes:

- Ansible host (this one controls the others via SSH+Ansible)
- Prometheus HA replica A
- Prometheus HA replica B
- Shared host: Pushgateway, Alertmanager, Grafana

Each host will also run the Node Exporter.

Make sure that each host allows you to SSH into it using keys (without password).

Make sure that Python 2 (2.6 or later) or Python 3 is installed on *all* machines:

    apt install python

### Install Ansible

SSH into the Ansible host with agent forwarding:

    ssh -A <ansible-host>

Install Ansible and prerequisites:

    apt-get -qq update
    apt install python-pip
    pip install ansible jmespath

### Clone Repository

Clone this demo setup repository:

    git clone https://github.com/juliusv/ansible_prometheus_demo
    cd ansible_prometheus_demo

### Install CloudAlchemy Prometheus playbooks

Install the prerequisite Ansible playbooks:

    ansible-galaxy install -r roles/requirements.yml

### Update the Ansible inventory file

Edit the `hosts` [Ansible inventory file](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) and replace the `<...>` placeholders with the IPs or hostnames of your corresponding VMs.

### Deploy Prometheus components

Deploy Prometheus replicas, Pushgateway, Alertmanager, Node Exporter, and Grafana:

    ansible-playbook site.yml

If everything worked, you should now be able to access:

- Prometheus replica A and B on their respective hosts on port 9090
- Pushgateway on port 9091
- Alertmanager on port 9093
- Grafana on port 3000

### Customizing the configuration

You can find the default settings for each of the component roles here:

- [Prometheus](https://github.com/cloudalchemy/ansible-prometheus/blob/master/defaults/main.yml)
- [Alertmanager](https://github.com/cloudalchemy/ansible-alertmanager/blob/master/defaults/main.yml)
- [Node Exporter](https://github.com/cloudalchemy/ansible-node-exporter/blob/master/defaults/main.yml)
- [Pushgateway](https://github.com/cloudalchemy/ansible-pushgateway/blob/master/defaults/main.yml)
- [Grafana](https://github.com/cloudalchemy/ansible-grafana/blob/master/defaults/main.yml)

All variable overrides are stored in the [`group_vars/all/vars`](group_vars/all/vars) directory in this repository. Change these to fit your needs.

You may also choose to split up the `group_vars/all` directory into multiple `group_vars/<group_name>` directories to isolate settings for each group into individual directories and files.

## Syntax-check changes

If you have made any local changes to the Ansible files, you can syntax-check them like this:

    ansible-playbook --syntax-check site.yml
