# Lateral movement and source-IP blocking

This is the standalone WMI/WinRM lab. I used Sysmon process lineage to detect a remote shell and Security Event 4624 to identify a source IP for an inbound firewall block.

The maintained [scenario, configuration and tests](https://github.com/LeeHoang26/ad-threat-detection-and-response-lab/tree/main/lateral-movement-containment) are in the consolidated lab. This repository keeps the standalone source and its recorded evidence.

## Setup

The lab used Wazuh 4.8 on Ubuntu 22.04, Windows Security/Sysmon telemetry and an isolated VMware network. DC01 used 192.168.50.10, WK01 192.168.50.20, the Manager 192.168.50.30 and the Kali test client 192.168.50.40.

## Detection and response

- Rule 100300 checks Sysmon Event 1 for WmiPrvSE.exe or wsmprovhost.exe spawning a shell.
- Event 4624 network logons supply source-IP context.
- The standalone worker uses recent-event correlation. The maintained handler requires the triggering logon ID and checks the resulting firewall rule.

Remote administration can produce the same process lineage. One source-IP block is not full host isolation.

Current response settings and failure-path tests are documented in the maintained scenario linked above. The Windows snippets there use local scope for the agent that generated the alert.

## Evidence

The initial WMI command returned hoang\administrator. After the inbound rule for 192.168.50.40, subsequent ping probes received no reply and another WMI connection timed out. The log wording describes the original run; the evidence establishes blocking from that IP.

![Sysmon Config Applied](assets/00_sysmon_config_applied.png)

![Wazuh Active Response Config](assets/00_wazuh_ossec_active_response_config.png)

![Initial WMI Execution](assets/01_initial_wmi_execution.png)

![Wazuh Rule 100300 Detection](assets/04_wazuh_rule_100300_detection.png)

![Wazuh Alert MITRE Details](assets/05_wazuh_alert_mitre_details.png)

![SOAR Containment Log](assets/02_soar_containment_log.png)

![Attacker Isolated Proof](assets/03_attacker_isolated_proof.png)

## Related lab

[Wazuh Detection Quality and Regression Lab](https://github.com/LeeHoang26/wazuh-detection-quality-lab) contains the offline corpus, expected outcomes and reports. Its precision and recall describe that corpus.

Le Duc Hoang
