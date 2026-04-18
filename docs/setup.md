# Lab Setup Guide

## 1. CML Requirements

- CML 2.7+ with license covering 15 nodes
- Required images:
  - `ubuntu-24-04-20250503`
  - `iol-xe-17-16-01a`
  - `ioll2-xe-17-16-01a`
  - `alpine-desktop-3-21-3`
  - `netbox-server`

## 2. Start the Lab

Import and start the ANSIBLELAB from CML. Wait for all nodes to boot (~3 minutes).

All management IPs should come up on `192.168.255.0/24` for routers and `192.168.250.0/24` for switches.

## 3. Set Up the Ansible Control Node

SSH into the Ubuntu-Ansible node:

```bash
ssh admin@192.168.255.81
```

Install Ansible and the Cisco IOS collection:

```bash
pip install ansible
ansible-galaxy collection install cisco.ios ansible.utils
```

Clone this repo:

```bash
git clone https://github.com/<your-username>/ansible-automation-lab.git
cd ansible-automation-lab
pip install -r requirements.txt
```

## 4. Configure Credentials

Create the vault password file:

```bash
echo "your_vault_password" > .vault_pass
chmod 600 .vault_pass
```

Encrypt your device password:

```bash
ansible-vault encrypt_string 'cisco' --name 'vault_device_password'
```

Paste the output into `inventory/group_vars/all.yml`.

## 5. Verify Connectivity

```bash
ansible all -m ping -i inventory/hosts.ini
```

All devices should return `pong`.

## 6. Run Playbooks

Full site deployment:
```bash
ansible-playbook playbooks/site.yml -i inventory/hosts.ini
```

Routers only:
```bash
ansible-playbook playbooks/configure_routers.yml -i inventory/hosts.ini
```

Switches only:
```bash
ansible-playbook playbooks/configure_switches.yml -i inventory/hosts.ini
```

Backup all configs:
```bash
ansible-playbook playbooks/backup_configs.yml -i inventory/hosts.ini
```

Validate the network:
```bash
ansible-playbook playbooks/validate_network.yml -i inventory/hosts.ini
```

## Troubleshooting

| Issue | Fix |
|---|---|
| Ansible can't reach device | Check `192.168.255.0/24` routing from Ubuntu-Ansible node |
| SSH refused | Verify `ip ssh version 2` and RSA key on device |
| OSPF not forming | Check `show ip ospf neighbor` — ensure network statements match |
| VLAN missing on switch | Re-run `configure_switches.yml` with `-v` for details |
| Vault error | Ensure `.vault_pass` file exists and matches the encrypted string |
