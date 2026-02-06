---
title: "Recursive Member Count for Distribution Lists"
date: 2026-02-06
draft: false
tags: ["graph-api", "exchange"]
description: "How to Count All Members in a Distribution List (Including Nested Groups)"
showtoc: false
---

# Get-GroupMemberReport

PowerShell script to audit Distribution Lists and Microsoft 365 Groups membership using Microsoft Graph API. Returns **transitive (recursive) member counts** - including all nested group members.

## Why?

- `Get-DistributionGroupMember` only returns direct members, not nested
- Exchange Online cmdlets have pagination timeouts on large tenants
- Graph API's `Get-MgGroupTransitiveMemberCount` solves both problems

## Requirements

- PowerShell 5.1+ or PowerShell 7+
- Microsoft Graph PowerShell SDK
- Permissions: `Group.Read.All`, `Directory.Read.All`

## Usage
```powershell
# Distribution Lists only
.\Get-GroupMemberReport.ps1 -DL

# Microsoft 365 Groups only  
.\Get-GroupMemberReport.ps1 -M365

# Both types
.\Get-GroupMemberReport.ps1 -All

# Test mode - first ~100 DLs or M365
.\Get-GroupMemberReport.ps1 -DL/M365 -TestLimit 100
```

## Output

CSV file with columns:

| Column | Description |
|--------|-------------|
| PrimarySmtpAddress | Group email address |
| DisplayName | Group display name |
| GroupType | `DL` or `M365` |
| Members | Total recursive member count |
| Empty | `yes` if 0 members |
| Size_1_10 | `yes` if 1-10 members |
| Size_11_100 | `yes` if 11-100 members |
| ... | etc. |

Size buckets make it easy to filter and analyze DL distribution in Excel.

## How it works

1. Fetches mail-enabled groups via Graph API
2. Filters by `groupTypes` property (`[]` = DL, `["Unified"]` = M365)
3. Calls `Get-MgGroupTransitiveMemberCount` for each group
4. Exports results with size category flags

## Get the script

Full source code with usage examples: [GitHub](https://github.com/przemekokon/Staff-from-blog/blob/main/Get-DLsMembershipCountReport.ps1)
