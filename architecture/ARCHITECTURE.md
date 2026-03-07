# Lab Architecture

## Network Diagram

```
                        ┌──────────────────────────────────────────────────────────┐
                        │                   HOST MACHINE                            │
                        │                (VirtualBox Hypervisor)                    │
                        │                                                           │
                        │  ┌────────────────────────────────────────────────────┐  │
                        │  │              Private Network: 10.10.10.0/24         │  │
                        │  │                                                     │  │
                        │  │  ┌──────────────┐  ┌──────────────┐  ┌──────────┐ │  │
                        │  │  │  ATTACKER    │  │   SENSOR     │  │FORENSICS │ │  │
                        │  │  │  Kali Linux  │  │ Ubuntu 22.04 │  │Ubuntu 22 │ │  │
                        │  │  │ 10.10.10.10  │  │ 10.10.10.20  │  │10.10.10.30│ │  │
                        │  │  │              │  │              │  │          │ │  │
                        │  │  │ Metasploit   │  │ Suricata 7.x │  │Velocirptr│ │  │
                        │  │  │ Impacket     │  │ Zeek 6.x     │  │Volatility│ │  │
                        │  │  │ Mimikatz     │  │ (SPAN/mirror)│  │Autopsy   │ │  │
                        │  │  │ Atomic RT    │  │              │  │Chainsaw  │ │  │
                        │  │  └──────┬───────┘  └──────┬───────┘  └────┬─────┘ │  │
                        │  │         │                  │               │        │  │
                        │  │         └──────────────────┴───────────────┘        │  │
                        │  │                       eth1 (promisc)                │  │
                        │  └────────────────────────────────────────────────────┘  │
                        │                                                           │
                        │  NAT Adapter (eth0) → Internet                           │
                        └──────────────────────────────────────────────────────────┘
```

## VM Specifications

| VM Name    | OS            | CPUs | RAM   | Disk  | IP           |
|------------|---------------|------|-------|-------|--------------|
| attacker   | Kali 2024.1   | 2    | 2 GB  | 40 GB | 10.10.10.10  |
| sensor     | Ubuntu 22.04  | 2    | 3 GB  | 40 GB | 10.10.10.20  |
| forensics  | Ubuntu 22.04  | 4    | 6 GB  | 80 GB | 10.10.10.30  |

## Data Flow

```
Attacker ──[malicious traffic]──► All VMs
                                      │
                              Sensor (SPAN tap)
                                 │        │
                           Suricata     Zeek
                           alerts/      logs/
                           EVE JSON     JSON
                                │
                            [Collected by]
                                │
                           Forensics VM
                           Velociraptor
                           (timeline + memory)
```

## Tool Ports Reference

| Service                  | Port  | Protocol |
|--------------------------|-------|----------|
| Velociraptor GUI         | 8889  | HTTPS    |
| Velociraptor Frontend    | 8000  | HTTPS    |
| Suricata stats           | 9595  | HTTP     |
| SSH (all VMs)            | 22    | SSH      |
| Metasploit handler       | 4443  | HTTPS    |
| Attacker HTTP server     | 8080  | HTTP     |

## Networking Notes

- `eth0` — NAT adapter on all VMs (internet access for tool installation)
- `eth1` — Host-only adapter `10.10.10.0/24` (lab traffic only)
- Sensor's `eth1` runs in **promiscuous mode** to capture all lab traffic
- Zeek and Suricata listen on `eth1`
