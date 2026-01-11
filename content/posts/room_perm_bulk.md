---
title: "Assign Calendar Permissions in Bulk"
date: 2026-01-10
draft: false
tags: ["powershell", "exchange"]
showtoc: false
---

## Just a simple one-liner

When you have many room mailboxes, granting someone access to each room calendar one-by-one is painful. This one-liner reads a list of room SMTP addresses from a text file and applies the same permission to each mailbox calendar.

```powershell

Get-Content .\rooms.txt | % {
  Write-Host "Working on $_" -ForegroundColor Cyan
  Add-MailboxFolderPermission -Identity "$($_.Trim()):\Calendar" -User "przemek@contoso.com" -AccessRights Reviewer
}
```
### Notes
- Text file we import via `Get-Content` contains a room email address per line
- `.Trim` is being used to remove blank spaces in TXT file - (' room@contoso.com' -> 'room@contoso.com') - it's just a safety feature I like to use
- In some tenants, the calendar folder name might be localized (for example "Kalendarz" in Poland, "Kalender" in  Germany and "Calendier" for France)
- If permissions are already granted for an user, you need to either remove these and re-add or use `Set-MailboxFolderPermissions`