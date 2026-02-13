---
title: "Creating and Configuring Entra Extension Attributes"
date: 2026-02-13
draft: false
tags: ["powershell", "microsoft-graph", "entra-id"]
showtoc: false
---

Microsoft Exchange spoiled us - mail-enabled objects always had a set of custom attributes ready to use out of the box. Entra ID is less generous, you have to extend the schema yourself from scratch.

## 1. Create an Application Registration

Application Registration works s a namespace for an extension attributes.
```powershell
New-MgApplication -DisplayName "Extension Attribute Sample Application" -SignInAudience AzureADMyOrg
```

## 2. Create an Extension Property

Now we create an attribute itself.
```powershell
New-MgApplicationExtensionProperty -ApplicationId <Id> -Name "Att1" -DataType String -TargetObjects @("Group")
```

## 3. Create a Service Principal

Without a Service Principal, the extension attributes are defined but cannot be used - the application exists only as a 'definition', not as an active entity in your tenant.
```powershell
New-MgServicePrincipal -AppId <AppId>
```

## 4. Verify the Extension Property
```powershell
Get-MgApplicationExtensionProperty -ApplicationId <Id> | ft Name
```

The extension attribute name follows this convention:
```
extension_{ServicePrincipalObjectIdWithoutHyphens}_{AttributeName}
```

Example:
```
extension_0bff5cf1c92f438e95aaab5fc59c0743_Att1
```

## 5. Set the Attribute on a Group

Prepare the body parameter using the full extension attribute name:
```powershell
$att1 = @{extension_0bff5cf1c92f438e95aaab5fc59c0743_Att1 = "Sample Value"}
```

Update the group:
```powershell
Update-MgGroup -GroupId <GroupId> -BodyParameter $att1
```

## 6. Verify the Result
```powershell
(Get-MgGroup -GroupId <GroupId>).AdditionalProperties | ft
```

## How it all fits together

{{< figure src="/images/ext_attribute.png" alt="Entra ID Extension Attributes - complete workflow" caption="The arrows show how Application ID and AppId flow through the subsequent commands" >}}

## Notes

- After creating the Service Principal, wait a few minutes for sync, before attempting to set any attribute values
- Step 2's `-TargetObjects` parameter supports multiple object types: `User`, `Group`, `AdministrativeUnit`, `Application`, `Device`, and `Organization`