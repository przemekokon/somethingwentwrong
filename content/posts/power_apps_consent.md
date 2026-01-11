---
title: "How to Get Rid of Power Apps Permissions Consent Form"
date: 2026-01-10
draft: false
tags: ["powershell", "powerapps"]
showtoc: false
---

## Permissions Consent Pop-up

Every new user must approve permissions when accessing an app. Existing users see this prompt again after app logic or connector updates.

 {{< figure src="/images/consent.png" alt="Power Apps Grant Consent" caption="App permissions consent pop-up" >}}

In most scenarios, this pop-up can be bypassed.

## Hiding consent pop-up

 This requries a single PowerShell command:
```powershell
 Set-AdminPowerAppApisToBypassConsent -EnvironmentName [Guid] -AppName [Guid]
 ````

Expected result:
 {{< figure src="/images/bypass_result.png" alt="Power Apps Grant Consent" >}}
 {{< figure src="/images/bypass.png" alt="Power Apps Grant Consent" caption="BypassConsent flag is set" >}}

 **We can get guids using PowerShell:**

Environment GUID: 
```powershell
 Get-AdminPowerAppEnvironment ['Environment Display Name'] | Select EnvironmentName
 ````

App GUID:
```powershell
Get-AdminPowerApp ['App Display Name'] -EnvironmentName [Guid] | Select AppName
```

## What happens under the hood?
After the command runs successfully, the BypassConsent flag is set to True for the app.

If the connector meets certain criteria (listed below), the app assumes consent is granted.

This does NOT grant tenant-level delegated access (like an Entra registered app). If a user lacks permissions to access data, the app still denies access.

> **In short:** The app behaves as the user already clicked "Allow".

**Consent bypassing criteria:**
- Works only for Microsoft connectors (except..)
- Does not work for Entra and Azure DevOps connectors
- Does not work for third-party connectors (like Slack or Smartsheet)
- Connectors must use OAuth or No Auth

## Notes

- These commands are available in module: `Microsoft.PowerApps.Administration.PowerShell`
- Command reversing this setting is 
```powershell
Clear-AdminPowerAppApisToBypassConsent -EnvironmentName [Guid] -AppName [Guid]
```
- After connector changes, updates, or major logic updates, it's good practice to reset the BypassConsent flag

---

## Summary

Use `Set-AdminPowerAppApisToBypassConsent` to hide consent pop-ups.  
This doesn't change permissions - just removes the prompt.
