# 🤖 Ansible Network Automation Lab

A multi-tier network automation lab built on **Cisco Modeling Labs (CML)** using **Ansible** to automate configuration management across IOS-XE routers and L2 switches, with **NetBox** as the source of truth and end-host reachability validation.

---

## 📐 Lab Topology

```
                   Management Network: 192.168.255.0/24
      ┌──────────────────────────────────────────────────────┐
      │  [Ubuntu-Ansible]──[S1]──[R1]──[R2]──[R3]          │
      │   192.168.255.81        .101   .102   .103          │
      │                [NetBox]  │             │            │
      └──────────────────────────┼─────────────┼────────────┘
                                 │             │
                   L2 Network: 192.168.250.0/24
              ┌──────────────────┐    ┌──────────────────┐
              │      S2 (.2)     │────│      S3 (.3)     │
              │  (IOL L2 Switch) │    │  (IOL L2 Switch) │
              └────────┬─────────┘    └──────────┬───────┘
                       │                          │
              ┌────────┴────────┐       ┌─────────┴───────┐
              │    S4 (.4)      │       │    S5 (.5)      │
              │ (Access Switch) │       │ (Access Switch) │
              └──┬──────────┬───┘       └──┬─────────┬────┘
                 │          │              │         │
              [PC-1]     [PC-2]         [PC-3]    [PC-4]
```

| Device | Role | OS/Image | Mgmt IP |
|---|---|---|---|
| Ubuntu-Ansible | Ansible Control Node | Ubuntu 24.04 | 192.168.255.81 |
| R1 | Core Router | IOL-XE 17.16.01a | 192.168.255.101 |
| R2 | Core Router | IOL-XE 17.16.01a | 192.168.255.102 |
| R3 | Core Router | IOL-XE 17.16.01a | 192.168.255.103 |
| S2 | Distribution Switch | IOL-L2-XE 17.16.01a | 192.168.250.2 |
| S3 | Distribution Switch | IOL-L2-XE 17.16.01a | 192.168.250.3 |
| S4 | Access Switch | IOL-L2-XE 17.16.01a | 192.168.250.4 |
| S5 | Access Switch | IOL-L2-XE 17.16.01a | 192.168.250.5 |
| PC-1 – PC-4 | End Hosts | Alpine Desktop | DHCP |
| NetBox | IPAM / Source of Truth | netbox-server | DHCP |

---

## 📁 Project Structure

```
ansible-automation-lab/
├── README.md
├── requirements.txt
├── ansible.cfg
├── .gitignore
│
├── inventory/
│   ├── hosts.ini           # Static inventory
│   └── group_vars/         # Group-level variables
│       ├── all.yml
│       ├── routers.yml
│       └── switches.yml
│
├── host_vars/              # Per-device variables
│   ├── R1.yml
│   ├── R2.yml
│   ├── R3.yml
│   ├── S2.yml
│   └── S3.yml
│
├── playbooks/
│   ├── site.yml            # Master playbook
│   ├── configure_routers.yml
│   ├── configure_switches.yml
│   ├── backup_configs.yml
│   └── validate_network.yml
│
├── roles/
│   ├── common/             # Shared tasks (SSH, hostname, NTP)
│   ├── router/             # Router-specific tasks (OSPF, interfaces)
│   └── switches/           # Switch tasks (VLANs, STP, trunks)
│
├── configs/
│   ├── routers/            # Reference configs for R1, R2, R3
│   └── switches/           # Reference configs for S2, S3, S4, S5
│
└── docs/
    └── setup.md
```

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8+, Ansible 2.14+
- SSH access to the Ubuntu-Ansible node (`192.168.255.81`)
- CML lab running with all nodes booted

### 1. Clone the repo
```bash
git clone https://github.com/<your-username>/ansible-automation-lab.git
cd ansible-automation-lab
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
ansible-galaxy collection install cisco.ios
```

### 3. Set your vault password
```bash
echo "your_vault_password" > .vault_pass
chmod 600 .vault_pass
```

### 4. Verify connectivity
```bash
ansible all -m ping -i inventory/hosts.ini
```

### 5. Run the full site playbook
```bash
ansible-playbook playbooks/site.yml -i inventory/hosts.ini
```

---

## 📋 Playbooks

| Playbook | Description |
|---|---|
| `site.yml` | Master playbook — runs all roles in order |
| `configure_routers.yml` | Applies OSPF, interfaces, and routing configs to R1-R3 |
| `configure_switches.yml` | Applies VLANs, trunks, and STP to S2-S5 |
| `backup_configs.yml` | Backs up running configs from all devices |
| `validate_network.yml` | Validates connectivity and expected state |

---

## 🧰 Technologies Used

| Tool | Purpose |
|---|---|
| [Cisco CML](https://www.cisco.com/c/en/us/products/cloud-systems-management/modeling-labs/index.html) | Network simulation platform |
| [Ansible](https://www.ansible.com/) | Agentless automation framework |
| [cisco.ios](https://docs.ansible.com/ansible/latest/collections/cisco/ios/) | Ansible collection for IOS/IOS-XE |
| [NetBox](https://netbox.dev/) | IPAM / source of truth |
| [Python 3](https://www.python.org/) | Scripting and Ansible runtime |

---

## 📄 License

[MIT](LICENSE)
