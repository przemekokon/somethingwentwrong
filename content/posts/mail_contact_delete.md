---
title: "Force Removal Orphaned Contact in Microsoft 365"
date: 2026-01-26
draft: false
tags: ["powershell", "exchange", "microsoft365", "graph-api"]
showtoc: false
---

## The Problem

Classic Exchange Online scenario - trying to remove an orphaned (maybe used to be synced) object.

{{< figure src="/images/contact_removal_error.png" alt="Error message when trying to remove contact" caption= "Getting slapped in the face with error" >}}

## The Solution

Two words: Graph API.

### Step 1: Get the Mail Contact ID

First, we need to identify the exact object we're dealing with:
```powershell
Get-MailContact lorem.ipsum@contoso.com | Format-List Id
```

This will give the unique identifier for the mail contact.

### Step 2: Verify the Object in Graph API

Better safe than sorry- let's do a double check. Replace `<ID>` with the ID from Step 1:
```powershell
Invoke-MgGraphRequest -Method GET -Uri "https://graph.microsoft.com/beta/directoryObjects/<ID>" -OutputType PSObject
```

Review the output to confirm this is indeed the object you want to remove.

### Step 3: Force Delete via Graph API

Exterminate.
```powershell
Invoke-MgGraphRequest -Method DELETE -Uri "https://graph.microsoft.com/beta/directoryObjects/<ID>"
```

## More or less important Notes

- Graph API deletions are immediate - there's no confirmation prompt, so triple-check that ID
- Here is how a sample uri looks like:
{{< figure src="/images/contact_removal_url.png" alt="Graph API URI structure showing directory objects endpoint" >}}
- URI - not URL (uniform resource identifier)

## Summary

Someday we will get `Remove-MgContact` but today is not the day. We must use a workaround.