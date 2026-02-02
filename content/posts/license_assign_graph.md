---
title: "Assign licenses in bulk via Graph API"
date: 2026-01-26
draft: false
tags: ["powershell", "microsoft365", "graph-api"]
showtoc: false
---

Add:

```powershell
Get-Content <file path> | foreach {Set-MgUserLicense -UserId $_ -AddLicenses @{SkuID = '2ced8a00...'} -RemoveLicenses @()}
```

Remove:

```powershell
Get-Content <file path> | foreach {Set-MgUserLicense -UserId $_ -RemoveLicenses @('3db7c7ead579...') -AddLicenses @{}}
```

Check for a license SkuId:

`Get-MgUserLicenseDetail -UserId <UPN>`or`Get-MgSubscribedSku | fl SkuPartNumber, skuid`

- File path is a TXT file containing UPNs one-per-line.