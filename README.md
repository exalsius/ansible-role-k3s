ansible-role-k3s
=========

This Ansible role installs and configures a multi-node K3s Kubernetes cluster on remote machines.
It supports setting up both control plane and worker nodes, enabling the deployment of lightweight Kubernetes environments.

Requirements
------------

Install the required Python dependencies using either pip or uv:

```bash
# with pip
pip install -r requirements.txt

# or with uv
uv pip install -r requirements.txt
```

Usage
------------
Inventory example

```ini
[all:vars]
ansible_user=ubuntu
fetch_dir_path=./fetched

[control]
control-node ansible_host=1.1.1.1

[worker]
worker-node ansible_host=2.2.2.2
```

Example playbook

```yml
---
- name: Install k3s
  hosts: all
  become: true
  vars:
    master_ip: "{{ hostvars[groups['control'][0]]['ansible_host'] }}"
  roles:
    - exalsius.k3s
```
Save this as install-k3s.yml and run it with:

```bash
ansible-playbook -i inventory.ini install-k3s.yml
```

Role Variables
--------------

| Variable         | Default Value              | Description                                                                 |
|------------------|----------------------------|-----------------------------------------------------------------------------|
| `k3s_version`    | `"v1.32.2+k3s1"`           | Version of K3s to install.                                                 |
| `systemd_dir`    | `/etc/systemd/system`      | Path to the systemd unit file directory.                                   |
| `token`          | `"changeme!"`              | Shared token used to join worker nodes to the cluster.                     |
| `ansible_user`   | `root`                     | SSH user used to connect to remote hosts.                                  |
| `install_name`   | `"k3s"`                    | Name of the K3s service (can be customized for variants).                  |
| `fetch_dir_path` | `"./fetched"`              | Local directory to fetch cluster-related files from remote control nodes.  |

Using This Role as a Dependency
--------------
To use the role from in another project, add it to your `requirements.yml` like this:

```yaml
# requirements.yml
- name: exalsius.k3s
  src: https://github.com/exalsius/ansible-role-k3s.git
  version: v1.0.0  # or "main", or a commit SHA
```

Then install the role using:

```bash
ansible-galaxy install -r requirements.yml
```

You can now use it in your playbooks like this:

```yaml
- name: Install k3s
  hosts: all
  become: true
  roles:
    - exalsius.k3s
```

Inventory Requirements
--------------


To use this role, your inventory must include at least:

- **At least one host in the `[control]` group** — this becomes the K3s control-plane node.
- **A variable number of hosts in the `[worker]` group** — these will join as agent nodes.

#### Example `inventory.ini`

```ini
[all:vars]
ansible_user=ubuntu
fetch_dir_path=./fetched

[control]
control-node ansible_host=1.1.1.1

[worker]
worker-node ansible_host=2.2.2.2
```
