---
title: "Assign a license via Graph API"
date: 2026-01-26
draft: false
tags: ["powershell", "microsoft365", "graph-api"]
showtoc: false
---

That's pretty simple:

Adding:

```powershell
Get-Content C:\Temp\mm.txt | foreach {Set-MgUserLicense -UserId $_ -AddLicenses @{SkuID = '2ced8a00-3ed1-4295-ab7c-57170ff28e58'} -RemoveLicenses @()}
```

Removal:

```powershell
Get-Content C:\Temp\mm.txt | foreach {Set-MgUserLicense -UserId $_ -RemoveLicenses @('b30411f5-fea1-4a59-9ad9-3db7c7ead579') -AddLicenses @{}}
```

Check for license SkuId

`Get-MgUserLicenseDetail -UserId <UPN>`
Or
`Get-MgSubscribedSku | fl SkuPartNumber, skuid`