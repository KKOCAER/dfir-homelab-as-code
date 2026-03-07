# PCAPs Directory

Place your packet capture files here for analysis exercises.

## Naming Convention

```
YYYY-MM-DD_<scenario>_<description>.pcap
```

Example:
```
2024-01-15_lateral-movement_smb-pth.pcap
2024-01-15_phishing_http-download.pcap
2024-01-16_c2_beacon-cobaltstrike.pcap
```

## Quick Analysis Commands

```bash
# File info
capinfos capture.pcap

# Protocol stats
tshark -r capture.pcap -q -z io,phs

# Extract HTTP objects
tshark -r capture.pcap --export-objects http,./extracted/

# Re-run through Zeek
zeek -r capture.pcap /opt/zeek/share/zeek/site/local.zeek

# Re-run through Suricata
suricata -r capture.pcap -l ./suricata-output/
```

## Public PCAP Resources

- [Malware Traffic Analysis](https://www.malware-traffic-analysis.net/)
- [PacketTotal](https://packettotal.com/)
- [Wireshark Sample Captures](https://wiki.wireshark.org/SampleCaptures)
- [NETRESEC PCAPs](https://www.netresec.com/?page=PcapFiles)
