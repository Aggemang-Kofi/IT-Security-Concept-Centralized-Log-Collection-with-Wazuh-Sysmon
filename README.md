# IT Security Concept: Centralized Log Collection with Wazuh & Sysmon

**Author:** Simon Agyemang-Kofi  
**Date:** May 2025  
**Target System:** Windows 10/11 Endpoints + Wazuh Server  
**Scope:** This document outlines the concept for deploying centralized log collection and monitoring using Sysmon, WEF, and Wazuh SIEM.

---

## 1. Objective
To ensure reliable collection and analysis of security-relevant Windows events for threat detection, compliance, and incident response.

---

## 2. Background
Windows systems often lack native, detailed logging capabilities. Sysmon provides granular process and network telemetry. Together with WEF (Windows Event Forwarding) and Wazuh SIEM, this setup enhances visibility and supports proactive threat detection.

---

## 3. System Architecture

**Components:**
- **Endpoints:** Windows 10/11 with Sysmon installed
- **Forwarders:** WEF for native log types (e.g., Security, Application)
- **SIEM:** Wazuh Server (Debian) with Filebeat and ELK stack

**Diagram:**
```
+-----------+     +------------+     +----------+     +-----------+
| Windows 10| --> | Event Fwd  | --> | Wazuh    | --> | Kibana    |
| + Sysmon  |     | (WEF/GPO)  |     | Logstash |     | Dashboard |
+-----------+     +------------+     +----------+     +-----------+
```

---

## 4. Implementation Steps
1. Install Sysmon with a hardened config (SwiftOnSecurity template)
2. Configure Windows Event Forwarding via GPO
3. Install Wazuh on a central Debian server
4. Connect Filebeat to forward logs from Wazuh to ELK
5. Configure Kibana dashboards and Wazuh rules

---

## 5. Security Goals
- **Integrity:** Logs are signed/stored in a tamper-resistant format
- **Availability:** Logs are centralized and redundantly stored
- **Confidentiality:** Log access restricted via RBAC in Wazuh/Kibana
- **Compliance:** Supports audit trails and detection for ISO 27001 and GDPR

---

## 6. Risk Assessment

| Risk | Mitigation |
|------|------------|
| Log loss due to network issue | Local caching via Filebeat |
| Misconfigured Sysmon | Use community-vetted config |
| Excessive noise/log volume | Tune Wazuh rules and Sysmon filters |

---

## 7. Maintenance Plan
- Review logs weekly via Kibana
- Update Sysmon configuration monthly
- Archive old logs quarterly to S3 / cold storage

---

## 8. Conclusion
This implementation strengthens Windows endpoint visibility and supports audit, compliance, and real-time threat monitoring.

---

## 9. Advanced Logging Configuration

**Enhanced Sources:**
- PowerShell ScriptBlock, Module Logging
- DNS query logging
- WMI/Scheduled Task event monitoring
- File Integrity Monitoring (FIM)

**GPOs Applied:**
- Enable Event IDs 4104, 4688, 4720, 7045, etc.

---

## 10. Custom Wazuh Rule Example

```xml
<rule id="100101" level="10">
  <if_sid>4697</if_sid>
  <match>Service Name: RemoteRegistry</match>
  <description>Potential lateral movement – RemoteRegistry service started</description>
</rule>
```

---

## 11. Threat Intelligence Integration

- Integrate with AbuseIPDB, AlienVault OTX
- Use Wazuh's active-response for dynamic blocking
- Map external IPs and hashes to internal activity

---

## 12. SIEM Dashboard Examples

- Process trees from Sysmon logs
- Timeline of user/service creation events
- GeoIP map of login attempts
- Rule ID heatmap (top triggered rules)

---

## 13. Security Incident Workflow

| Step | Action |
|------|--------|
| Detection | Wazuh alert (e.g., rule ID 100101) |
| Triage | Analyst reviews service/IP |
| Containment | Disable account/service |
| Eradication | Remove persistence mechanisms |
| Recovery | Patch, restore service |
| Lessons Learned | RCA + Wazuh rule tuning |

---

## 14. Compliance Mapping

- **ISO/IEC 27001**: A.12.4.1 Logging and Monitoring
- **GDPR**: Article 32 - Security of processing
- **NIST SP 800-53**: AU-6 - Audit Review and Analysis
