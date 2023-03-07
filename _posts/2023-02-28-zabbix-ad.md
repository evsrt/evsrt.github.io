---
title: Zabbix-AD
date: 2023-02-28 12:01:10 +0300
categories: [microsoft, ad]
tags: [zabbix, ad]     # TAG names should always be lowercase
#img_path: /img/
---
# Zabbix template for mon security events from AD
```yaml
template:
  metadata:
    name: Windows Server Active Directory Security Event Audit
    description: This template monitors security events in a Windows Server Active Directory environment
    version: 1.0

  triggers:
    - name: Failed Logon Attempts
      expression: {Template App Windows AD Security:security.evtfail[Security,EventLog,DC01,4771].last(1)}=1
      severity: High
      description: "Number of failed logon attempts in the last 5 minutes exceeds the threshold"

    - name: Account Lockouts
      expression: {Template App Windows AD Security:security.evtfail[Security,EventLog,DC01,4740].last(1)}=1
      severity: High
      description: "Number of account lockouts in the last 5 minutes exceeds the threshold"

    - name: Privilege Escalation
      expression: {Template App Windows AD Security:security.evtfail[Security,EventLog,DC01,4624].last(1)}=1 and {Template App Windows AD Security:security.evtmsg[Security,EventLog,DC01,4624,4625,4626,4672].str("Privilege escalation detected")}=1
      severity: High
      description: "Privilege escalation detected in the last 5 minutes"

  items:
    - name: Failed Logon Attempts
      type: Zabbix Agent (Active)
      key: security.evtfail[Security,EventLog,DC01,4771]
      value_type: Count
      description: "Number of failed logon attempts in the last 5 minutes"

    - name: Account Lockouts
      type: Zabbix Agent (Active)
      key: security.evtfail[Security,EventLog,DC01,4740]
      value_type: Count
      description: "Number of account lockouts in the last 5 minutes"

    - name: Privilege Escalation
      type: Zabbix Agent (Active)
      key: security.evtmsg[Security,EventLog,DC01,4624,4625,4626,4672].str("Privilege escalation detected")
      value_type: Log
      description: "Privilege escalation detected in the last 5 minutes"
```
> In this example, the template includes three triggers that monitor for failed logon attempts, account lockouts, and privilege escalation events in the Security event log of a Windows Server Active Directory domain controller named DC01. The triggers are set to generate alerts with a high severity level if the specified conditions are met.

> The template also includes three items that collect data from the Security event log using the security.evtfail and security.evtmsg keys. The security.evtfail key retrieves the count of failed logon attempts and account lockouts, while the security.evtmsg key searches for the string "Privilege escalation detected" in the event messages of specific event IDs related to privilege escalation.