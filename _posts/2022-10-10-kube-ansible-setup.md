---
layout: post
title: "Using Ansible with Kubernetes"
date: 2022-10-10 17:00:00 -0500
categories: [homelab,ansible,kubernetes]
tags: [homelab,kubernetes,ansible,management,python]
---
## Overview

While Kubernetes is a powerful platform, management of the platform can be challenging especially as the number of clusters increase. There are a variety of tools help manage this. Ansible is a well known open source tool in the configuration management space. This post will go over how to get started in using Kubernetes with Ansible.

## Dependencies

### Server Side (Ansible server)

* [Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-ubuntu)

Ansible Galaxy Package

* [Ansible Kubernetes module documentation](https://docs.ansible.com/ansible/latest/collections/kubernetes/core/index.html#plugins-in-kubernetes-core)

```bash
ansible-galaxy collection install kubernetes.core
```

As explained in this [Ansible documentation](https://docs.ansible.com/ansible/latest/reference_appendices/python_3_support.html#using-python-3-on-the-managed-machines-with-commands-and-playbooks) the ansible python interpreter does detect and automatically use Python 3 on many platforms. However, for those that do not, use the following ways to explicitly configure it.

If you see some output similar to this when you run a playbook:

```console
TASK [Get Cluster information] ************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: ImportError: No module named kubernetes.dynamic
fatal: [c1-master1@192.168.120.138]: FAILED! => {"changed": false, "error": "No module named kubernetes.dynamic", "msg": "Failed to import the required Python library (kubernetes) on c1-master1's Python /usr/bin/python. Please read the module documentation and install it in the appropriate location. If the required library is installed, but Ansible is using the wrong Python interpreter, please consult the documentation on ansible_python_interpreter"} 
```

 You will know you need to configure either of the following:

```yaml
# Example inventory that makes an alias for localhost that uses Python3
localhost-py3 ansible_host=localhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3

# Example of setting a group of hosts to use Python3
[py3_hosts]
ubuntu16
fedora27

[py3_hosts:vars]
ansible_python_interpreter=/usr/bin/python3
```

```console
# ansible localhost-py3 -m ping
# ansible-playbook sample-playbook.yml
```

Add the "-e 'ansible_python_interpreter=/usr/bin/python3'" flag at the end of the command

```console
ansible localhost -m ping -e 'ansible_python_interpreter=/usr/bin/python3'
ansible-playbook sample-playbook.yml -e 'ansible_python_interpreter=/usr/bin/python3'
```

### Client Side (Management node)

Install pip3

```bash
sudo apt install pip3
```

```bash
pip3 install kubernetes
```
