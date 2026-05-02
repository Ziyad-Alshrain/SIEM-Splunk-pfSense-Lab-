# Screenshots

This folder contains visual evidence of the SIEM Splunk lab setup, log collection, detection engineering, attack simulation, and alerting workflow.

## Phase 1: Infrastructure Setup

- pfSense interfaces
- Firewall rules

## Phase 2: Log Collection

- Splunk receiving port (9997)
- Forwarder active connection
- Initial Windows logs in Splunk

## Phase 3: Registry Persistence Detection

- Windows registry audit policy configuration
- Registry auditing configuration on `HKCU\Software`
- Simulated persistence using a malicious Run key
- Splunk detection of EventCode 4657
- Registry detection search query
- Alert configuration for suspicious registry activity

## Screenshot List

| File | Description |
|---|---|
| `audit-policy.png` | Windows Audit Registry policy enabled |
| `registry-auditing-config.png` | Registry auditing configured on HKCU\Software |
| `malicious-run-key.png` | Simulated persistence using Run key `evil` |
| `registry-event-detected.png` | Splunk detected EventCode 4657 showing the `evil` registry value |
| `splunk-registry-search.png` | SPL search used to detect registry Run key modifications |
| `registry-alert-config.png` | Splunk alert configuration for registry modification detection |
