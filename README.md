# cybersecurity_ops_content.md

# Enterprise Security Operations & IAM Automation Framework

A technical repository documenting automation pipelines, Active Directory maintenance workflows, and Identity Governance strategies for modern enterprise SecOps.

---

## 1. Automated Identity Governance & Access Reviews

### Overview
Transitioning manual access reviews into a continuous enterprise framework using automated audit pipelines reduces human error, prevents privilege creep, and ensures compliance with minimal operational friction.

### Pipeline Architecture Workflow
1. **Data Ingestion:** Extract active user and group data from Active Directory infrastructure.
2. **Triage & Normalization:** Utilize custom parsing scripts to flag anomalous permissions, circular references, and inactive accounts.
3. **Automated Ticketing:** Route compliance exceptions directly into ServiceNow ITSM with pre-prioritized severity levels.
4. **Validation & Logging:** Cross-reference operational logs via Splunk to audit changes and generate continuous compliance reports.

---

## 2. Active Directory Infrastructure & Hygiene

### Best Practices for Organizational Unit (OU) Cleanups
* **Orphaned Object Identification:** Regularly scan for service accounts and user objects that have exceeded active threshold limits (e.g., inactive for >90 days).
* **Global Address List (GAL) Optimization:** Maintain clean distribution lists and secure nested group memberships to prevent security leakage.
* **Resolving Circular Referencing:** 
  * *The Issue:* Circular nesting causes infinite loops during access evaluations, severely impacting authentication performance and directory replication.
  * *The Fix:* Implement automated validation checks in PowerShell deployment scripts to detect and block circular group assignments prior to production commit.

---

## 3. SecOps Automation & Incident Triaging Script (PowerShell Blueprint)

```powershell
<#
.SYNOPSIS
    Automated triage script for parsing operational alerts and routing tickets.
.DESCRIPTION
    Queries local logs, filters anomalies based on threshold parameters, 
    and formats data for automated queue management.
#>

$ThresholdMinutes = 15$LogSource = "Security"

Write-Host "Initiating automated triage scan at $(Get-Date)..." -ForegroundColor Cyan

# Example logic for scanning recent high-severity events
$RecentEvents = Get-WinEvent -LogName$LogSource -MaxEvents 50 | Where-Object { $_.TimeCreated -ge (Get-Date).AddMinutes(-$ThresholdMinutes) }

foreach ($Event in$RecentEvents) {
    if ($Event.Id -eq 4720) { # Account creation event ID example
        Write-Host "[ALERT] New user account created: $($Event.Properties[0].Value)" -ForegroundColor Yellow
        # Insert ServiceNow ticket generation API hook or logging routine here
    }
}

Write-Host "Scan completed successfully." -ForegroundColor Green
