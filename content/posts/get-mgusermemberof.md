---
title: "Which groups do users belong to? A Graph API approach"
date: 2026-03-23
draft: false
tags: ["powershell", "graph-api", "entraID", "exchange"]
showtoc: false
---

## What's that?

Just another script for listing users and their group memberships.
You feed it a text file with UPNs, it queries Microsoft Graph, and spits out a flat CSV like:

```csv
UserPrincipalName,       GroupName,              GroupEmail,             GroupType
user1@company.com,       Some Distribution List, some-dl@company.com,   DistributionList
user1@company.com,       Security Group ABC,     sg-abc@company.com,    MailSecurityGroup
user2@company.com,       Some Distribution List, some-dl@company.com,   DistributionList
```

## Where's it?

[On GitHub](https://github.com/przemekokon/Staff-from-blog/blob/main/Get-MgUserMemberOf.ps1)