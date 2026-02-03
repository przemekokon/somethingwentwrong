---
title: "When PowerAutomate Can't Regex - Let AI Do The Parsing"
date: 2026-02-03
draft: false
tags: ["powerautomate", "AI", "automation"]
description: "How to use AI prompts in Power Automate to extract data from emails when regex isn't an option"
showtoc: true
---

## Problem

I need to monitor my mailbox for certain ticket emails and extract specific data from them. Classic automation stuff.

The catch? Power Automate doesn't support regex.

## The Workaround

Power Automate has this "Run a prompt" action that lets you use GPT to process text. 

The idea is simple:
1. Get the email
2. Feed it to AI with instructions
3. Get structured JSON back
4. Do whatever you want with clean data

## General flow description

The flow has the following steps:

1. Get an email from a specific mailbox folder (emails get there via inbox rule)
2. Convert HTML (email body) into TEXT (for raw output)
3. Run a prompt to get specific info from the email
4. Save the prompt output into JSON to easily proceed
5. In my scenario I use a switch as I work with two types of emails
6. If the email is about DL - save a new row into DL sheet
7. If the email is about a group mailbox - save a new row into GroupMailboxes sheet

## The Flow

Here's the general structure:

{{< figure src="/images/flow_general.png" alt="Power Automate flow overview" caption="The full flow - nothing too crazy" >}}

Let's break down each step.

## When a new email arrives (V3)

I have an inbox rule that moves specific emails to a dedicated folder. The flow just monitors that folder.

You could also filter directly in Power Automate using subject/from conditions, but inbox rules give you more flexibility.

{{< figure src="/images/flow_email.png" alt="Email trigger configuration" caption="Monitoring a specific folder keeps things clean" >}}

## HTML to text

Email bodies are HTML. AI works better with plain text. This step does the conversion.

{{< figure src="/images/flow_html.png" alt="HTML to text action" caption="Just pass the email body - nothing fancy" >}}

## Run a prompt

This is where the magic happens. A few important things to follow:

**The prompt needs to be strict.** AI loves to be helpful and chatty. You don't want "Here are the extracted values based on your email content:". You want just the JSON.

**Use JSON output mode.** There's an "Output: JSON" setting in the prompt configuration. Use it. This makes Power Automate automatically parse the response into structured fields.

**Use the standard model, not mini.** Mini works, but standard works better.

**Add content = data source.** The "Add content" button is where you connect your input data. In my case, it's the plain text email body from the previous step.

**Test multiple times.** AI can be... creative. Run your prompt with different test inputs before going live.

{{< figure src="/images/flow_prompt_general.png" alt="Run a prompt configuration" caption="The prompt action with JSON output enabled" >}}

{{< figure src="/images/flow_prompt_details.png" alt="Prompt details and model selection" caption="I recommend using the standard model, not mini" >}}

Here's the prompt I ended up with:

```text
Based on the email content, extract the following information and return ONLY a JSON object with no additional text:

{
  "CatalogTaskNumber": "extracted SCTASK number or null",
  "RequestType": "DL or GroupMailbox",
  "DLName": "name of distribution list if RequestType is DL, otherwise null",
  "MailboxAddress": "email address if RequestType is GroupMailbox, otherwise null"
}

Rules:
- RequestType must be exactly "DL" or "GroupMailbox" based on the request
- DLName can be either a name or an address
- Return only valid JSON, no markdown, no explanation
```

## Parse JSON

Here's where it gets weird.

So the prompt already returns JSON. And Power Automate parses it automatically when you use JSON output mode. So why would you add another "Parse JSON" action to parse.. JSON with JSON?

Good question.

But here's what happened: the automatic parsing worked fine for DL emails. For GroupMailbox emails `RequestType` was `null`. Even though the raw JSON response clearly had the correct value.

**The fix:** Add a "Parse JSON" action manually and use the raw `Text` field from the prompt response. Define your own schema with proper null handling.

{{< figure src="/images/flow_parse.png" alt="Parse JSON configuration" caption="Parse the Text output, not the structured fields" >}}

For content, use: 'Text' - basically a prompt response.

Schema (with null support):

```json
{
  "type": "object",
  "properties": {
    "CatalogTaskNumber": {
      "type": ["string", "null"]
    },
    "RequestType": {
      "type": ["string", "null"]
    },
    "DLName": {
      "type": ["string", "null"]
    },
    "MailboxAddress": {
      "type": ["string", "null"]
    }
  }
}
```
You can build your own schema based on output from the prompt and using sample payload to generate it.
## Switch

In my case, I have two types of requests that need to go to different Excel files. The Switch action handles this based on `RequestType`.

{{< figure src="/images/flow_switch_all.png" alt="Switch action for routing" caption="DL goes one way, GroupMailbox goes another" >}}

Important: Use the expression `body('Parse_JSON')?['RequestType']` - not just `body('Parse_JSON')` (that returns the whole object and Switch will complain about expecting a string).

The cases are `DL` and `GroupMailbox` - case-sensitive, so make sure your prompt returns exactly these values.

## Add a row into a table

Standard Excel action. Map the JSON fields to your table columns.

{{< figure src="/images/flow_excel.png" alt="Excel add row action" caption="JSON fields map directly to Excel columns" >}}

## Summary

Emails can be easily accessed using Power Automate. Excel tables can be easily written using Power Automate. The issue starts when a specific part of an email should be written into Excel - regex would be helpful, if supported.

It's even quite funny, we got a working AI in Power Automate faster than regex support..