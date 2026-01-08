---
title: "Updating Distribution List Extended Attributes with PowerShell"
date: 2026-01-02
draft: false
tags: ["powershell", "exchange", "on-prem"]
---

## The easy part

Creating distribution lists in bulk is quite simple.

**Just a CSV file:**
```csv
name,mail
Fancy Group,Fancy.Group@example.com
Another Fancy Group,Another.Fancy.Group@example.com
Not That Fancy Group,Not.That.Fancy.Group@example.com
```


**And for-each loop**
```powershell
Import-Csv .\groups.csv | ForEach-Object {
    New-DistributionGroup 
    -Name $_.name 
    -PrimarySmtpAddress $_.mail 
    -OrganizationalUnit 'OU=Groups,DC=wrong,DC=went,DC=something,DC=dev'
    -Type Security 
}
```

**Result:**

| Name | DisplayName | GroupType | PrimarySmtpAddress |
|------|-----------|-----------|-------------------|
| Fancy Group | Fancy Group | Universal, SecurityEnabled | Fancy.Group@example.com |
| Another Fancy Group | Another Fancy Group | Universal, SecurityEnabled | Another.Fancy.Group@example.com |
| Not That Fancy Group | Not That Fancy Group | Universal, SecurityEnabled | Not.That.Fancy.Group@example.com |

## But updating msExchExtensionAttribute is different


A below won't work as there is no such command under Set-DistributionGroup.
```powershell
Set-DistributionGroup -Identity "Fancy Group" -msExchExtensionAttribute20 "Lorem Ipsum"
```

For this matter we must use `Set-ADGroup` but it accepts these for Identity parameter:

- A distinguished name
- A GUID (objectGUID)
- A security identifier (objectSid)
- A SAM account name (sAMAccountName)

 Since Active Directory adds random numbers to the sAMAccountName for uniqueness, we cannot reliably query by the DL "name":

 {{< figure src="/images/random_numbers3.png" alt="Active Directory group with random numbers" caption="Example: 'Another Fancy Group' becomes 'Another Fancy Group-1-292223989'" >}}


A proper command, for the Another Fancy Group is:
```powershell
 Set-ADGroup -Identity 'Another Fancy Group-1-292223989' -Replace @{msExchExtensionAttribute20="Lorem Ipsum"}
```
We can also use filter and pipeline:

```powershell
Get-ADGroup -Filter "mail -eq 'Another.Fancy.Group@example.com'" |
Set-ADGroup -Replace @{msExchExtensionAttribute20="Lorem Ipsum"}
```
## What about bulk update?

For multiple groups, we can import a CSV file for data input, query objects by mail attribute and update these accordingly in loop.

```powershell
#CSV file should contain 'mail' header

$groups = Import-Csv .\groups.csv

foreach ($g in $groups) {

    $group = Get-ADGroup -Filter "mail -eq '$($g.mail)'" -Properties mail

    if ($group) {
        Set-ADGroup -Identity $group.DistinguishedName -Replace @{msExchExtensionAttribute20 = "Lorem Ipsum"}
        Write-Host "OK: $($g.mail) -> msExchExtensionAttribute20 has been set" -ForegroundColor Green
    }
    else {
        Write-Host "ERROR: DL not found $($g.mail)" -ForegroundColor Red
    }
}
```

We can filter for any other property - as long as it's uniqe (and covered in a CSV). Just update script's line 7.

## Summary

This is a hybrid/on-premises scenario - less common now days, but still relevant for organizations running Exchange hybrid or syncing on-prem AD to Entra ID.
