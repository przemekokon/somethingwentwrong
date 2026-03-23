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

| UserPrincipalName | PrimarySmtpAddress | GroupName | GroupEmail | GroupType |
|---|---|---|---|---|
| user1@abc.com | user1@abc.com | Some DL | some-dl@abc.com | DistributionList |
| user1@abc.com | user1@abc.com | Security Group ABC | sg-abc@abc.com | MailSecurityGroup |
| user2@abc.com | user2@abc.com | Some DL | some-dl@abc.com | DistributionList |

## Where's it?

[On GitHub](https://github.com/przemekokon/Staff-from-blog/blob/main/Get-MgUserMemberOf.ps1)

## Requirements

- PowerShell 5.1+ or PowerShell 7+
- Microsoft Graph PowerShell SDK
- Permissions: `User.Read.All`, `GroupMember.Read.All`, `Group.Read.All`