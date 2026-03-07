# Incident Report Template (SANS PICERL Model)

---

## Cover Page

| Field              | Value                         |
|--------------------|-------------------------------|
| **Incident ID**    | IR-XXXX-XXXX                  |
| **Classification** | TLP:RED / TLP:AMBER / TLP:GREEN |
| **Severity**       | Critical / High / Medium / Low|
| **Status**         | Open / Contained / Closed     |
| **Lead Analyst**   |                               |
| **Date Opened**    |                               |
| **Date Closed**    |                               |

---

## Executive Summary

> 2–3 sentence non-technical summary of what happened, the impact, and the outcome.

---

## 1. Preparation

### Detection Source
- [ ] IDS/IPS Alert (Suricata)
- [ ] Network Anomaly (Zeek)
- [ ] EDR Alert (Velociraptor)
- [ ] User Report
- [ ] Threat Intelligence Feed
- [ ] External Notification

### Detection Timeline
| Time (UTC)   | Event                              |
|--------------|------------------------------------|
|              | Alert triggered                    |
|              | Analyst assigned                   |
|              | Incident declared                  |

---

## 2. Identification

### Affected Systems

| Hostname   | IP Address   | OS       | Role        | Compromised? |
|------------|--------------|----------|-------------|-------------|
|            |              |          |             | Yes / No    |

### Attack Vector
- [ ] Email / Phishing
- [ ] Web / Drive-by
- [ ] Supply Chain
- [ ] Insider Threat
- [ ] Exposed Service
- [ ] Physical

### Initial Indicator
```
[Paste raw alert / log entry that triggered investigation]
```

### MITRE ATT&CK Techniques Identified

| Tactic | Technique | ID | Observed Evidence |
|--------|-----------|-----|-------------------|
|        |           |     |                   |

---

## 3. Containment

### Short-term Containment Actions
| Time (UTC) | Action Taken                     | Performed By |
|------------|----------------------------------|--------------|
|            | Host isolated from network       |              |
|            | Firewall rule applied            |              |
|            | Account disabled                 |              |

### Evidence Preservation (pre-remediation)
- [ ] Memory dump acquired (hash: `                  `)
- [ ] Disk image acquired (hash: `                  `)
- [ ] Log exports completed
- [ ] Network captures saved

---

## 4. Eradication

| Action                              | Completed | Notes |
|-------------------------------------|-----------|-------|
| Malware removed / quarantined       | ☐         |       |
| Persistence mechanisms removed      | ☐         |       |
| Compromised accounts reset          | ☐         |       |
| Lateral movement paths closed       | ☐         |       |
| Vulnerability patched               | ☐         |       |

---

## 5. Recovery

| Action                              | Completed | Date       |
|-------------------------------------|-----------|------------|
| System rebuilt / restored           | ☐         |            |
| Credentials rotated                 | ☐         |            |
| Monitoring enhanced                 | ☐         |            |
| System returned to production       | ☐         |            |

---

## 6. Lessons Learned

### What went well?
1. 
2. 

### What could be improved?
1. 
2. 

### Gaps identified
| Gap                              | Recommended Fix               | Priority |
|----------------------------------|-------------------------------|----------|
|                                  |                               |          |

### Detection Improvements
```yaml
# New Suricata rules, Zeek scripts, or Velociraptor artifacts to add
```

---

## Appendix A: IOC List

```
# IP Addresses

# Hashes (MD5 | SHA256 | Filename)

# Domains

# Registry Keys

# File Paths
```

## Appendix B: Raw Evidence References

| ID  | Type         | Description         | Storage Path                        |
|-----|--------------|---------------------|-------------------------------------|
| E01 | Memory Dump  |                     | `/datasets/memory-dumps/`           |
| E02 | PCAP         |                     | `/datasets/pcaps/`                  |
| E03 | EVTX Export  |                     | `/investigations/incident-reports/` |

## Appendix C: Timeline

> See [`investigations/timeline-analysis/IR-XXXX-timeline.csv`](../timeline-analysis/)
