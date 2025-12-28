---
title: "Something Went Wrong - And That's The Point"
date: 2026-01-08
draft: false
tags: ["meta", "announcement"]
categories: ["blog"]
description: "Why this blog exists and what you can expect"
---

## Welcome to SomethingWentWrong.dev

If you work in IT long enough, you develop a special relationship with error messages.

That moment when you confidently run a script in production, only to see:
```powershell
Get-Mailbox : The operation couldn't be performed because 
object 'user@domain.com' couldn't be found on 'DC01.domain.local'.
```

Or worse - when it **doesn't** error, but the output looks like this:
```
@{DisplayName=John Doe}
@{DisplayName=Jane Smith}
```

Because you forgot `-ExpandProperty`. Again.

## What This Blog Is About

I'm an IT architect working with M365, PowerShell, and Power Platform at scale. 

This blog documents:
- **Real problems** from production environments
- **Step-by-step solutions** (not just "here's the final script")
- **What went wrong** along the way
- **Lessons learned** (usually the hard way)

## What You Won't Find Here

- Generic "10 PowerShell tips" listicles
- Copy-pasted Microsoft docs
- Solutions that only work in demo environments
- Perfect code that never fails

## What You Will Find

- Detailed walkthroughs of complex projects
- Power Apps patterns that actually scale
- Exchange Online automation that handles errors
- DMARC implementations that don't break email
- Honest discussions of what **didn't** work

## Coming Soon

I'm working on a series about building an enterprise vacation calendar in Power Apps. 

Spoiler: **A lot went wrong.** But that's what makes it worth documenting.

---

*Have a project that went sideways? I'd love to hear about it - find me on [LinkedIn](your-link) or [GitHub](your-link).*