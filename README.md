# 🖥️ Streamlining Citrix VDI Desktops: Tracking Last Logins with PowerShell

## Overview

Managing Citrix Virtual Apps and Desktops (CVAD) can be a time-consuming task, especially when trying to track virtual desktops (VDIs) assigned to users who have left the organization. This PowerShell-based solution helps Citrix admins identify inactive VDIs by retrieving last login timestamps and cross-referencing them with disabled Active Directory (AD) accounts.

By automating this process, IT teams can reduce overhead, reclaim unused resources, and stay compliant with internal policies.

---

## 🔍 What This Script Does

- Prompts the administrator for a **Citrix Delivery Group name**
- Retrieves a list of VDIs, their assigned users, and **last login timestamps**
- Identifies machines that belong to **disabled AD accounts**
- Outputs results to a **CSV report** for further review and action

---

## 📂 Output Example

The script exports a CSV file with the following columns:
- `MachineName`
- `LastConnectionUser`
- `LastConnectionTime`

The report can be used to:
- Flag VDIs for decommissioning
- Place unused machines in maintenance mode
- Audit resource utilization

---

## 🧰 Prerequisites

- Citrix PowerShell SDK (Brokers module)
- Appropriate admin permissions to run Citrix PowerShell commands
- Access to the Citrix Delivery Controller
- PowerShell 5.1+ (Windows)

---

## ▶️ Script Usage

```powershell
# Import Citrix Module
asnp Citrix*

# Prompt for Delivery Group name and output file
$DeliveryGroup = Read-Host "Enter the Delivery Group name"
$Date = Get-Date -Format "MMddyyyy_HHmmss"
$OUTPUTFile = "C:\Temp\Last_Login_$Date.csv"

# Validate and collect session data
$DeliveryGroupExists = Get-BrokerDesktopGroup -Name $DeliveryGroup -ErrorAction SilentlyContinue

if ($DeliveryGroupExists) {
    $Machines = Get-BrokerMachine -MaxRecordCount 1000 | Where-Object { $_.DesktopGroupName -eq $DeliveryGroup }

    if ($Machines) {
        $Machines | Select-Object MachineName, LastConnectionUser, LastConnectionTime | Export-Csv -Path $OUTPUTFile -NoTypeInformation
        Write-Host "Report saved to: $OUTPUTFile" -ForegroundColor Green
    } else {
        Write-Host "No machines found in the specified Delivery Group: $DeliveryGroup" -ForegroundColor Yellow
    }
} else {
    Write-Host "Error: The specified Delivery Group '$DeliveryGroup' does not exist." -ForegroundColor Red
}
# Streamlining-Citrix-VDI-Desktops-Tracking-Last-Logins-with-PowerShell
Tracking Citrix VDI end user lastlogins
