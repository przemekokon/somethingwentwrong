---
title: "How to Update Proxy Addresses for a Cloud-Only Object"
date: 2026-03-16
draft: false
tags: ["powershell", "graph-api", "entraID"]
showtoc: false
---

## Problem

If you want to add or remove (basically update) a proxy address on a cloud-only account - without an Exchange mailbox - you cannot.

There is no such option in the GUI and PowerShell won't allow it either, throwing an error:
```
Set-MgUser : Property 'proxyAddresses' is read-only and cannot be set.
Status: 400 (BadRequest)
ErrorCode: Request_BadRequest
```

## Solution

Use the Graph API **BETA** endpoint.

- **Method:** PATCH  
- **URL:** `https://graph.microsoft.com/beta/users/<User_ID>`  
- **Request Body:**
```json
{
    "proxyAddresses": [
        "SMTP:PrimarySMTPAddress",
        "smtp:Alias1",
        "smtp:Alias2",
        "smtp:Alias3"
    ]
}
```

- **Expected Response:** `204 No Content`

{{< figure src="/images/proxy_update.png" alt="Graph Explorer" caption="An example" >}}

## Tip

You can first retrieve the existing proxy addresses and paste them into the Request Body, then add or remove the entries you need.
```powershell
https://graph.microsoft.com/beta/users/<User_ID>?$select=proxyAddresses
```

{{< figure src="/images/proxy_update2.png" alt="Graph Explorer" caption="An example" >}}