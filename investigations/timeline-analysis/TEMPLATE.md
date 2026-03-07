# Timeline Analysis Template

## Incident Reference: IR-XXXX-XXXX
**Analyst:** ________________  
**Date:** ________________  
**Scope:** ________________  

---

## 1. Evidence Sources

| Source              | Hash (SHA256)                    | Collected   | Chain of Custody |
|---------------------|----------------------------------|-------------|-----------------|
| Memory dump         |                                  |             | ✅ / ❌           |
| Disk image          |                                  |             | ✅ / ❌           |
| EVTX logs           |                                  |             | ✅ / ❌           |
| PCAP                |                                  |             | ✅ / ❌           |
| Zeek logs           |                                  |             | ✅ / ❌           |
| Suricata alerts     |                                  |             | ✅ / ❌           |

---

## 2. Plaso Timeline Generation

```bash
# Step 1: Parse all evidence into Plaso storage
log2timeline.py \
  --storage-file /investigations/timeline-analysis/IR-XXXX.plaso \
  --parsers win7 \
  /mnt/evidence/

# Step 2: Process into a super timeline
psort.py \
  -o l2tcsv \
  -w /investigations/timeline-analysis/IR-XXXX-timeline.csv \
  /investigations/timeline-analysis/IR-XXXX.plaso

# Step 3: Filter to relevant window
psort.py \
  -o l2tcsv \
  --slice "2024-01-15T08:00:00" \
  --slice_size 1440 \
  /investigations/timeline-analysis/IR-XXXX.plaso \
  -w /investigations/timeline-analysis/IR-XXXX-filtered.csv
```

---

## 3. Hayabusa Windows Event Timeline

```bash
# Generate timeline from EVTX
hayabusa csv-timeline \
  -d /mnt/evidence/evtx/ \
  -o /investigations/timeline-analysis/IR-XXXX-hayabusa.csv \
  --all-events \
  --UTC

# HTML report
hayabusa html-report \
  -d /mnt/evidence/evtx/ \
  -o /investigations/timeline-analysis/IR-XXXX-report.html
```

---

## 4. Master Timeline

> **TZ:** UTC | **Format:** ISO 8601

| Timestamp (UTC)         | Source     | Event Description                        | MITRE ID   | Significance |
|-------------------------|------------|------------------------------------------|------------|--------------|
| 2024-MM-DDTHH:MM:SSZ   |            |                                          |            | 🔴 High      |
| 2024-MM-DDTHH:MM:SSZ   |            |                                          |            | 🟡 Med       |
| 2024-MM-DDTHH:MM:SSZ   |            |                                          |            | 🟢 Low       |

### Key Event Phases

#### 🎯 Initial Access
```
[TIMESTAMP] [SOURCE] [EVENT]
```

#### 🔧 Execution
```
[TIMESTAMP] [SOURCE] [EVENT]
```

#### 🛡️ Defense Evasion
```
[TIMESTAMP] [SOURCE] [EVENT]
```

#### 🔑 Credential Access
```
[TIMESTAMP] [SOURCE] [EVENT]
```

#### 🌐 Lateral Movement
```
[TIMESTAMP] [SOURCE] [EVENT]
```

#### 📤 Exfiltration / Impact
```
[TIMESTAMP] [SOURCE] [EVENT]
```

---

## 5. Indicators of Compromise (IOCs)

### IP Addresses
```
# Attacker C2
10.10.10.10

# Contacted external IPs
```

### File Hashes
```
# MD5 | SHA256 | Filename | Path
```

### Domain Names
```
# C2 domains / phishing domains
```

### Registry Keys
```
# Persistence mechanisms
HKCU\Software\Microsoft\Windows\CurrentVersion\Run\
```

### File Paths
```
# Dropped malware / tools
C:\Users\Public\
C:\Windows\Temp\
```

---

## 6. Analyst Notes

```
[Free-form notes, hypotheses, open questions]
```

---

## 7. Recommendations

| Priority | Recommendation                       | Owner      | Due Date |
|----------|--------------------------------------|------------|----------|
| P1       |                                      |            |          |
| P2       |                                      |            |          |
| P3       |                                      |            |          |
