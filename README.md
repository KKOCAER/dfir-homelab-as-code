# 🔍 DFIR Lab as Code

> A fully automated **Digital Forensics & Incident Response** lab environment provisioned with Vagrant + Ansible.

---

## 📐 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Host Machine                          │
│                                                             │
│  ┌─────────────┐   ┌──────────────┐   ┌────────────────┐  │
│  │  Attacker   │   │    Sensor    │   │   Forensics    │  │
│  │  Kali Linux │   │  Ubuntu 22   │   │  Ubuntu 22     │  │
│  │ 10.10.10.10 │   │ 10.10.10.20  │   │ 10.10.10.30    │  │
│  │             │   │ Suricata     │   │ Velociraptor   │  │
│  │ Metasploit  │   │ Zeek         │   │ Volatility3    │  │
│  │ Mimikatz    │   │ SPAN Port    │   │ Autopsy        │  │
│  └─────────────┘   └──────────────┘   └────────────────┘  │
│         │                  │                   │            │
│         └──────────────────┴───────────────────┘            │
│                      NAT Network                            │
└─────────────────────────────────────────────────────────────┘
```

See [`architecture/lab-diagram.png`](architecture/lab-diagram.png) for the full diagram.

---

## 🧰 Stack

| Component       | Tool                      | Role              |
|----------------|---------------------------|-------------------|
| Virtualisation | VirtualBox + Vagrant      | VM provisioning   |
| Config Mgmt    | Ansible                   | Automated setup   |
| IDS/IPS        | Suricata 7.x              | Network detection |
| NSM            | Zeek 6.x                  | Protocol analysis |
| EDR/DFIR       | Velociraptor              | Endpoint forensics|
| Memory         | Volatility 3              | RAM analysis      |
| Disk           | Autopsy / Sleuth Kit      | Disk forensics    |
| Attack Sim     | Metasploit / Atomic RT    | Red team          |

---

## ⚡ Quick Start

### Prerequisites

```bash
# macOS
brew install vagrant virtualbox ansible

# Ubuntu/Debian
sudo apt install vagrant virtualbox ansible
```

### Spin up the lab

```bash
git clone https://github.com/youruser/dfir-lab-as-code
cd dfir-lab-as-code

# Start all VMs
vagrant up

# Provision with Ansible (if not auto-provisioned)
cd ansible
ansible-playbook -i inventory playbooks/suricata.yml
ansible-playbook -i inventory playbooks/zeek.yml
ansible-playbook -i inventory playbooks/velociraptor.yml
ansible-playbook -i inventory playbooks/forensic_tools.yml
```

### Verify

```bash
vagrant status
vagrant ssh sensor    # SSH into sensor VM
vagrant ssh forensics # SSH into forensics VM
vagrant ssh attacker  # SSH into attacker VM
```

---

## 📁 Repository Structure

```
dfir-lab-as-code/
├── README.md
├── architecture/          # Network diagrams & design docs
├── vagrant/               # VM definitions (Vagrantfile)
├── ansible/
│   ├── inventory          # Host definitions
│   ├── playbooks/         # Per-tool playbooks
│   └── roles/             # Reusable Ansible roles
│       ├── sensor/        # Suricata + Zeek config
│       ├── forensics/     # Velociraptor, Volatility, Autopsy
│       └── attacker/      # Kali + offensive tooling
├── attack-scenarios/      # Documented attack playbooks
│   ├── phishing/
│   ├── powershell/
│   ├── credential-dump/
│   └── lateral-movement/
├── datasets/              # Sample evidence files
│   ├── pcaps/
│   └── memory-dumps/
└── investigations/        # Investigation templates & reports
    ├── timeline-analysis/
    └── incident-reports/
```

---

## 🎯 Attack Scenarios

| Scenario            | MITRE ATT&CK Techniques         | Script |
|--------------------|----------------------------------|--------|
| Phishing            | T1566.001, T1204.002            | [`attack-scenarios/phishing/`](attack-scenarios/phishing/) |
| PowerShell Abuse    | T1059.001, T1086                | [`attack-scenarios/powershell/`](attack-scenarios/powershell/) |
| Credential Dump     | T1003.001 (LSASS), T1003.003   | [`attack-scenarios/credential-dump/`](attack-scenarios/credential-dump/) |
| Lateral Movement    | T1021.001, T1550.002            | [`attack-scenarios/lateral-movement/`](attack-scenarios/lateral-movement/) |

---

## 🔬 Investigations

See [`investigations/`](investigations/) for:
- Pre-built timeline analysis templates
- Incident report templates (following SANS PICERL model)
- Sample IOC lists

---

## 🛡️ Network Layout

| VM         | IP           | OS           | Purpose             |
|------------|--------------|--------------|---------------------|
| attacker   | 10.10.10.10  | Kali 2024.1  | Red team simulation |
| sensor     | 10.10.10.20  | Ubuntu 22.04 | IDS/NSM (Suricata + Zeek) |
| forensics  | 10.10.10.30  | Ubuntu 22.04 | DFIR workstation    |

---

## ⚠️ Disclaimer

> This lab is intended **for educational and research purposes only**.  
> Run it in an isolated environment. Never use these techniques against systems you do not own or have explicit written permission to test.

---

## 📜 License

MIT — see [LICENSE](LICENSE)
