# Screenshots

This folder contains visual evidence from the SIEM Splunk lab, including setup, log collection, detection engineering, attack simulation, ransomware response, and recovery.

## Phase 1: Infrastructure Setup

- pfSense interfaces
- Firewall rules

## Phase 2: Log Collection

- Splunk server running
- Splunk receiving port enabled
- Windows Universal Forwarder connected
- Initial Windows logs visible in Splunk

## Phase 3: Detection Engineering

- Windows registry audit policy enabled
- Registry auditing configured on `HKCU\Software`
- Registry persistence detection query created
- Splunk alert configured for suspicious registry activity

## Phase 4: Attack Simulation and Validation

- Malicious Run key created for testing
- Registry modification detected in Splunk
- EventCode `4657` confirmed the detection logic worked

## Phase 5: Ransomware Incident Response

- WannaCry sample staged inside the isolated Windows VM
- Ransom note displayed after execution
- WannaCry-related files and artifacts created
- Encrypted file evidence collected
- Splunk detected ransomware-related process execution
- pfSense containment rule applied
- Microsoft Defender detected the ransomware
- Microsoft Defender quarantined the threat
- Windows VM restored from a clean snapshot

## Screenshot List

| File | Description |
|---|---|
| `pfsense-interfaces.png` | pfSense interface configuration |
| `firewall-rules.png` | Firewall rules used for network segmentation |
| `splunk-server-running.png` | Splunk Enterprise running on Ubuntu |
| `forwarder-active.png` | Windows Universal Forwarder connected to Splunk |
| `splunk-logs.png` | Initial Windows logs visible in Splunk |
| `audit-policy.png` | Windows Audit Registry policy enabled |
| `registry-auditing-config.png` | Registry auditing configured on `HKCU\Software` |
| `splunk-registry-search.png` | SPL search used to detect registry Run key modifications |
| `registry-alert-config.png` | Splunk alert configuration for registry modification detection |
| `malicious-run-key.png` | Simulated persistence using Run key `evil` |
| `registry-event-detected.png` | Splunk detected EventCode `4657` showing the `evil` registry value |
| `wannacry-sample-before-execution.png` | WannaCry sample staged before execution inside the Windows VM |
| `wannacry-ransom-note.png` | WannaCry ransom note displayed after execution |
| `wannacry-dropped-files.png` | WannaCry-related files and artifacts created on the endpoint |
| `encrypted-file-evidence.png` | Example of an encrypted file with unreadable content |
| `wannacry-process-execution.png` | Splunk EventCode `4688` showing ransomware-related process execution |
| `pfsense-ransomware-containment-rule.png` | pfSense firewall rule used to isolate the infected Windows VM |
| `defender-ransomware-detection.png` | Microsoft Defender detection of WannaCry ransomware |
| `defender-ransomware-quarantine.png` | Microsoft Defender quarantine action |
| `recovery-after-snapshot.png` | Windows VM restored from a clean snapshot after the incident |
