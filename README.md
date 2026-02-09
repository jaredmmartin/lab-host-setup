# lab-host-setup

An Ansible playbook to configure an Ubuntu Linux system as a lab environment host. This playbook manages configuration of the host used to run the environments in my other repositories, such as the [kubernetes-lab](https://github.com/jaredmmartin/kubernetes-lab).

## Overview

The Ansible playbook applies the following configuration:

- Update all OS packages
  - Report if a reboot is required
- Install Ansible components
  - Install packages
    - `ansible-lint`
  - Collections
    - `amazon.aws`
    - `ansible.posix`
    - `community.aws`
    - `community.general`
    - `community.windows`
    - `kubernetes.core`
    - `microsoft.ad`
    - `netbox.netbox`
- Install Docker
  - Add Docker repository signing key
  - Add Docker repository
  - Install packages
    - `containerd.io`
    - `docker-ce`
    - `docker-ce-cli`
    - `docker-compose-plugin`
- Install Git
  - `git-all`
  - If `github_user_config_enable` variable is `true`
    - Set git `user.name` from `github_user_name`
    - Set git `user.email` from `github_user_email`
- Install HashiCorp apps
  - Add HashiCorp repository signing key
  - Add HashiCorp repository
  - Install packages
    - `packer`
    - `terraform`
    - `vagrant`
      - Vagrant plugins
        - `vagrant-disksize`
        - `vagrant-reload`
        - `vagrant-vbguest`
- Install and configure NFS
  - Install packages
    - `nfs-kernel-server`
    - `nfs-common`
  - If `nfs_mounts` variable has items
    - Create `nfs-local-path`
    - Add mount
- Install and configure OpenSSH
  - Install packages
    - `openssh-server`
  - Create `.ssh` directory in user home
  - Create SSH keypair
- Install PowerShell
  - Install packages
    - `apt-transport-https`
    - `ca-certificates`
    - `curl`
    - `software-properties-common`
  - Add Microsoft repository signing key
  - Download Microsoft repository installer
  - Install Microsoft repository
  - Add Microsoft repository
  - Install `powershell`
- If `prometheus_exporter_enable` is `true`, install Prometheus Node Exporter
  - Download node exporter
  - Extract node exporter
  - Create `node_exporter` service
  - Start service
- Install Python
  - Install packages
    - `build-essential`
    - `libbz2-dev`
    - `libc6-dev`
    - `libffi-dev`
    - `libgdbm-dev`
    - `libncursesw5-dev`
    - `libsqlite3-dev`
    - `libssl-dev`
    - `tk-dev`
    - `wget`
    - `zlib1g-dev`
  - Add Python repository
  - Install packages
    - `python3.12`
    - `python3-pip`
    - `python3-passlib`
- If `telegraf_syslog_enable` is `true`, configure syslog forwarding to Telegraf
  - Template `templates/50-telegraf.conf` to `/etc/rsyslog.d/50-telegraf.conf"`
  - Restart `rsyslog` service
- Install VirtualBox
  - Add VirtualBox repository signing key
  - Add VirtualBox repository
  - Install `virtualbox-7.0`

## Requirements

- Ubuntu Linux 24.04

## Usage

1. Set variable values in `vars/main.yml`:
    1. `github_user_config_enable` (bool): Switch to enable global Git config setup
    1. `github_user_name` (string): Git `user.name` to set
    1. `github_user_email` (string): Git `user.email` to set
    1. `nfs_mounts` (list): NFS shares to mount
    1. `prometheus_exporter_enable` (bool): Switch to install and start Prometheus Node Exporter
    1. `telegraf_syslog_enable` (bool): Switch to configure rsyslog forwarding to Telegraf
    1. `telegraf_server_ip` (string): Telegraf server IP/host for rsyslog
    1. `telegraf_server_port` (int): Telegraf syslog port

1. Apply the playbook

    ```bash
    sudo ansible-playbook main.yml
    ```
