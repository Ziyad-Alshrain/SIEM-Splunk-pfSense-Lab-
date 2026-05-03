# SIEM-Splunk-pfSense-Lab

Hands-on SOC lab using Splunk Enterprise, pfSense, and Windows endpoint telemetry.

## Overview

This project is a hands-on Security Operations Center (SOC) lab built using Splunk Enterprise, pfSense firewall, and a Windows endpoint.

The goal was to build a small but realistic SOC environment and walk through the full workflow: setting up the lab, collecting logs, building detections, validating them through attack simulation, and responding to a ransomware incident.

This lab focuses on understanding how the tools work together in a practical blue team setup, not just installing them.

## Lab Architecture

The lab includes three main systems:

- Splunk Server (Ubuntu): `10.10.10.10`
- Windows Client: `10.10.30.10`
- pfSense Firewall: used for network segmentation and traffic control

Network segmentation was implemented to isolate systems and control communication between subnets.

## Phase 1: Infrastructure Setup

This phase focused on building the foundation of the lab.

Tasks completed:

- Created three virtual machines: Ubuntu, Windows, and pfSense
- Configured segmented networks using pfSense
- Applied firewall rules to control traffic between subnets

At this stage, the environment was ready for log collection and monitoring.

### pfSense Interfaces

![pfSense Interfaces](screenshots/pfsense-interfaces.png)

### Firewall Rules

![Firewall Rules](screenshots/firewall-rules.png)

## Phase 2: Log Collection

The goal of this phase was to get meaningful Windows logs into Splunk.

Tasks completed:

- Installed Splunk Enterprise on Ubuntu
- Enabled Splunk receiving on port `9997`
- Installed Splunk Universal Forwarder on the Windows endpoint
- Collected Windows Event Logs:
  - Security
  - System
  - Application

### Splunk Server Status

![Splunk Server](screenshots/splunk-server-running.png)

### Forwarder Connection

![Forwarder Active](screenshots/forwarder-active.png)

### Splunk Logs

![Splunk Logs](screenshots/splunk-logs.png)

This phase confirmed that the Windows endpoint was successfully sending logs to Splunk.

## Phase 3: Detection Engineering

After confirming that Windows logs were reaching Splunk, the next step was to turn raw events into useful detections.

The first detection focused on registry-based persistence. This is a common technique where malware or an attacker adds a value under Windows Run keys so a program starts automatically when the user logs in.

### Registry Persistence Detection

Windows Registry auditing was enabled on the endpoint to generate security events when registry keys or values are modified.

![Audit Policy](screenshots/audit-policy.png)

Registry auditing was then configured on `HKCU\Software` to monitor changes under the user registry hive.

![Registry Auditing Configuration](screenshots/registry-auditing-config.png)

The detection focused on suspicious changes to Run and RunOnce registry keys.

```spl
index=* EventCode=4657
(
    Object_Name="*\\CurrentVersion\\Run*"
    OR Object_Name="*\\CurrentVersion\\RunOnce*"
)
| where NOT Process_Name="*explorer.exe"
```

![Splunk Registry Search](screenshots/splunk-registry-search.png)

A Splunk alert was configured to trigger when suspicious registry persistence activity is detected.

![Registry Alert Config](screenshots/registry-alert-config.png)

## Phase 4: Attack Simulation and Validation

To test the detection, I simulated a simple registry persistence technique on the Windows endpoint.

A new registry value named `evil` was created under the Windows Run key:

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

The value was configured to run:

```text
C:\Windows\System32\cmd.exe
```

This registry location is commonly abused because values placed under the Run key can execute when the user logs in.

![Malicious Run Key](screenshots/malicious-run-key.png)

After creating and modifying the registry value, Splunk captured the activity as Windows Security EventCode `4657`.

![Registry Event Detected](screenshots/registry-event-detected.png)

The event showed the registry path, the value name `evil`, the new value data, and the process responsible for the change. This confirmed that the auditing configuration, Splunk forwarding, search logic, and alerting workflow were working correctly.

## Phase 5: Ransomware Incident Response

In this phase, a WannaCry ransomware sample was executed inside the isolated Windows VM to simulate a ransomware incident and practice the response workflow.

The goal was to observe the behavior, collect evidence, contain the host, review logs, remove the threat, and recover from a clean snapshot.

### Initial Execution

Before execution, the sample was placed inside the Windows VM along with test files used to observe the impact.

![WannaCry Sample Before Execution](screenshots/wannacry-sample-before-execution.png)

After execution, the WannaCry ransom note appeared on the endpoint, confirming active ransomware behavior.

![WannaCry Ransom Note](screenshots/wannacry-ransom-note.png)

### Impact and Artifacts

The ransomware created several WannaCry-related files and artifacts on the desktop, including `.wnry` files and `@WanaDecryptor@` components.

![WannaCry Dropped Files](screenshots/wannacry-dropped-files.png)

An affected file was opened after execution. The file content appeared unreadable, confirming that the original data had been encrypted.

![Encrypted File Evidence](screenshots/encrypted-file-evidence.png)

### Splunk Detection

Splunk captured Windows Security EventCode `4688`, showing process execution related to the ransomware activity.

The observed processes included:

- `taskdl.exe`
- `taskse.exe`
- `@WanaDecryptor@.exe`

![WannaCry Process Execution](screenshots/wannacry-process-execution.png)

This confirmed that Windows process creation auditing and Splunk log ingestion were working as expected.

### Containment

After confirming ransomware execution, the infected Windows VM was isolated using pfSense firewall rules to prevent further communication and possible lateral movement.

![pfSense Ransomware Containment](screenshots/pfsense-ransomware-containment-rule.png)

### Eradication

Microsoft Defender detected the ransomware as `Ransom:Win32/WannaCrypt` and generated a ransomware alert.

![Defender Ransomware Detection](screenshots/defender-ransomware-detection.png)

The detected ransomware artifact was then quarantined by Microsoft Defender.

![Defender Ransomware Quarantine](screenshots/defender-ransomware-quarantine.png)

### Recovery

Because the encrypted files could not be safely decrypted, recovery was done by restoring the Windows VM from a clean snapshot instead of attempting manual decryption.

![Recovery After Snapshot](screenshots/recovery-after-snapshot.png)

### Lessons Learned

This phase showed that ransomware response is more than just detecting malware. A complete response includes confirming execution, identifying impact, isolating the affected host, reviewing security logs, removing the threat, and recovering from a trusted backup or snapshot.

Key takeaways:

- Ransomware should be handled as a high-severity incident.
- Immediate containment is important to reduce the risk of spread.
- Process creation logs are useful for identifying malware execution.
- Endpoint protection and SIEM visibility work better together.
- Backups and snapshots are essential when encrypted files cannot be safely decrypted.

## Key Skills Demonstrated

- SIEM deployment and configuration using Splunk Enterprise
- Windows log collection using Splunk Universal Forwarder
- Windows Event Log analysis
- Detection engineering using SPL
- Registry persistence detection
- Process execution monitoring with EventCode `4688`
- Alert creation and tuning in Splunk
- Network segmentation using pfSense
- Ransomware behavior analysis
- Incident response workflow: detection, containment, eradication, and recovery
- Snapshot-based recovery after ransomware impact
