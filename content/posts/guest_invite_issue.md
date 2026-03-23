---
title: "Guest Account Invite Issue"
date: 2026-03-23
draft: false
tags: ["EntraID"]
description: "Guest invite failing - Please configure B2B collaboration"
showtoc: false
---

## Problem

Recently, one of our users got this error while inviting a guest to our tenant:

{{< figure src="/images/guest_2.png" alt="Error" caption="Error in your face" >}}

> Please configure B2B collaboration settings correctly and troubleshoot first, "https://aka.ms/b2b-troubleshoot". Error from Entra B2B: At least one invitation failed. Error: ResponseStatusNotOK, message: Group email address is not supported.


Looks like a tenant-wide issue, right?

## Solution

There isn't one - distribution lists cannot be invited as guest accounts.

## Afterword

The admin sees a clean, simple message directly in Entra ID under **Invite External User** - the user gets a wall of text.

{{< figure src="/images/guest_1.png" alt="Group email info" caption="What the admin sees" >}}