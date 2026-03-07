# Memory Dumps Directory

Store raw memory images here for Volatility3 analysis exercises.

## Naming Convention

```
YYYY-MM-DD_<hostname>_<OS>_<scenario>.raw
```

## Acquisition Tools

### Windows
```powershell
# WinPmem (open source, recommended)
winpmem_mini_x64_rc2.exe memory.raw

# DumpIt
DumpIt.exe /output memory.raw /quiet

# Magnet RAM Capture (GUI)
# https://www.magnetforensics.com/resources/magnet-ram-capture/
```

### Linux
```bash
# AVML (Azure VM forensics, works on bare metal too)
sudo avml memory.lime

# LiME (Loadable Kernel Module)
sudo insmod lime.ko "path=/tmp/memory.lime format=lime"
```

## Analysis Quick Start

```bash
vol3=/opt/volatility3/vol.py
IMAGE=/datasets/memory-dumps/victim.raw

# System info
python3 $vol3 -f $IMAGE windows.info

# Process list
python3 $vol3 -f $IMAGE windows.pslist
python3 $vol3 -f $IMAGE windows.pstree

# Network connections
python3 $vol3 -f $IMAGE windows.netstat

# Injected code detection
python3 $vol3 -f $IMAGE windows.malfind

# Dump suspicious process (PID 1234)
python3 $vol3 -f $IMAGE windows.dumpfiles --pid 1234

# Command history
python3 $vol3 -f $IMAGE windows.cmdline
python3 $vol3 -f $IMAGE windows.consoles

# Registry
python3 $vol3 -f $IMAGE windows.registry.hivelist
python3 $vol3 -f $IMAGE windows.registry.printkey --key "SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

## Public Memory Image Resources

- [MemLabs CTF Images](https://github.com/stuxnet999/MemLabs)
- [Volatility Foundation Samples](https://github.com/volatilityfoundation/volatility/wiki/Memory-Samples)
- [SANS FOR508 Practice Images](https://www.sans.org/cyber-security-courses/advanced-incident-response-threat-hunting-training/)
