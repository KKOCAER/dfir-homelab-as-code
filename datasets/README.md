# Datasets

This directory holds sample evidence files for practice investigations.

---

## pcaps/

Sample packet captures for network forensics exercises.

### Included datasets

| File                          | Description                                | Source       |
|-------------------------------|--------------------------------------------|--------------|
| `malware-traffic-analysis.pcap` | Drive-by download + C2 beacon            | Lab generated |
| `smb-lateral-movement.pcap`   | SMB PtH lateral movement traffic           | Lab generated |
| `dns-exfiltration.pcap`       | Data exfiltration via DNS TXT records      | Lab generated |
| `kerberos-roasting.pcap`      | AS-REP Roasting captured traffic           | Lab generated |

### Download public PCAP datasets

```bash
# Malware Traffic Analysis (exercises)
wget https://www.malware-traffic-analysis.net/2024/training-exercises.pcap.zip \
  -O pcaps/mta-exercises.pcap.zip

# SANS Holiday Hack datasets
# https://www.sans.org/mlp/holiday-hack-challenge/

# Wireshark sample captures
wget https://wiki.wireshark.org/uploads/SampleCaptures/http.pcap \
  -O pcaps/http-sample.pcap
```

### Analyzing PCAPs with Zeek

```bash
# Re-process a PCAP through Zeek offline
zeek -r pcaps/smb-lateral-movement.pcap \
  /opt/zeek/share/zeek/site/local.zeek \
  Log::default_logdir=./zeek-output

# Quick tshark overview
tshark -r pcaps/malware-traffic-analysis.pcap \
  -q -z io,phs          # protocol hierarchy stats

tshark -r pcaps/dns-exfiltration.pcap \
  -Y "dns.qry.type == 16" \
  -T fields -e dns.qry.name  # DNS TXT queries
```

---

## memory-dumps/

Memory images for Volatility3 practice.

### Creating a memory dump

```bash
# On a live Windows system (admin required)
# Using WinPmem
winpmem_mini_x64_rc2.exe victim.raw

# Using DumpIt
DumpIt.exe /output victim.raw

# On Linux VM (using Avml)
sudo avml /datasets/memory-dumps/linux-victim.lime
```

### Sample analysis workflow

```bash
cd /datasets/memory-dumps/

# 1. Image info
vol3 -f victim.raw windows.info

# 2. Running processes
vol3 -f victim.raw windows.pslist
vol3 -f victim.raw windows.pstree

# 3. Network connections
vol3 -f victim.raw windows.netstat

# 4. Find injected code
vol3 -f victim.raw windows.malfind

# 5. Dump suspicious process
vol3 -f victim.raw windows.dumpfiles --pid 1234

# 6. Registry hives
vol3 -f victim.raw windows.registry.hivelist
```

### Public memory images

```bash
# CTF memory images (MemLabs)
# https://github.com/stuxnet999/MemLabs

# Volatility Foundation test images
# https://github.com/volatilityfoundation/volatility/wiki/Memory-Samples
```
