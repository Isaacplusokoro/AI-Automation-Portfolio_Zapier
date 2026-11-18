# AI Inquiry Classifier and Router in Zapier: Portfolio Case Study

*By Okiemute Isaac Okoro**| Practice project built during the TS Academy AI & Automation course | November 2026*

> **Note:** This is a learning project built with a fictional scenario. Email addresses are placeholders and no real client data was used.

## Overview

This project is a working no-code automation: a Google Form submission is read by AI, classified into a category, logged to a spreadsheet, and routed by email to the right team. It follows the core pattern of AI automation: **trigger, AI judgment, action, guardrails, monitoring**.

## Concepts Applied

| Concept | How it appeared in this project |
| --- | --- |
| Automation basics | Trigger (form submitted), condition (category check), action (log and email) |
| AI in the middle | The AI step replaces a rigid keyword rule with judgment about meaning and tone |
| Prompt engineering | A clear task, fixed categories, a fallback, and a structured output format |
| Data structures | Each form response is a record of fields; AI output fields were mapped into spreadsheet columns |
| Guardrails | Confidence threshold, human review, no processing of sensitive documents, logging |

---

## The Goal

Automatically read incoming inquiries, classify them with AI, record every result, and send sales inquiries to the sales inbox, without a person sorting each message by hand.

## Architecture

**Google Form submission, then AI by Zapier (Generate Text) classifies the message, then a row is logged in Google Sheets, then Paths route the inquiry and an email is sent.**

## Design Decisions

- **Categories:** Sales, Support, KYC, Spam, and Other, with an "Uncertain" result when confidence is below 70%.
- **Structured output:** the AI returns Category, Confidence, and Action in a fixed format that later steps can read.
- **Guardrails designed in:**
  - KYC messages are forwarded untouched and never summarized, because they may contain sensitive documents.
  - Suspected spam goes to a human-review folder instead of being deleted.
  - Anything uncertain is flagged for manual review instead of guessed.
  - Every submission is logged with timestamp, category, confidence, and action.

### AI step prompt (version used)

```
Read this message: {{Your message field from the form}}

Classify it as one of: Sales, Support, KYC, Spam, Other.

If Sales: output 'Forward to sales'.
If Support: check if this FAQ text answers it: [FAQ TEXT]. If yes, draft a reply. If no, output 'Forward to support'.
If KYC: output 'Forward to customer care - do not process'.
If Spam: output 'Flag for spam review'.
If Other: output 'Flag for manual review'.

If you are less than 70% confident in the classification, output 'Uncertain - flag for manual review' instead.

Output format: Category | Confidence % | Action or Draft Reply
```

## Build Steps

1. Created a Google Form (message and email fields) as the trigger, then submitted a real test response so Zapier could detect it.
2. Added an AI step with the classification prompt.
3. Logged each result to a Google Sheet with the columns Timestamp, Category, Confidence, Action, and Message, mapping each AI output field to its column.
4. Added **Paths**: a Sales path that emails the sales team, and a second path for everything else.
5. Turned the Zap on and tested with several messages.

## Problems Debugged

| Problem | Diagnosis | Fix |
| --- | --- | --- |
| Output full of unwanted fields (dates, sentiment, entities) | Chose the "Extract Information" template, which has built-in fields | Switched to a free-form prompt action |
| Output used its own generic categories | Chose the "Classify" template | Used a plain text-in, text-out action so my own categories applied |
| Form not selectable in the trigger | Dropdown had not loaded and no response existed yet | Submitted a test response, refreshed the dropdown, and checked the connected Google account |
| Testing the email step showed the very first message | Step tests reuse cached sample data | Submitted fresh responses through the live form |
| Every step showed success but no email arrived | The second path had not been finished, so routing was incomplete | Completed both paths and re-tested |
| Email step missing from Zap History | The path condition was not matching, so the step never ran | Checked the Category rule value and confirmed the email action sat inside the path |

## Results

- A vague test message was classified as **Other** and flagged for manual review.
- A clear sales message ("I want to know about your pricing plans") was classified as **Sales** at 95% confidence, logged to the sheet, and emailed to the sales address.
- A non-sales message was routed down the second path as intended.
- Both branches were confirmed working end to end.

## Key Takeaways

- The **choice of action type** matters as much as the prompt.
- **Zap History** is the best debugging tool: it shows which steps ran, skipped, or failed.
- "Success" on every step does not prove the right path ran, so check the data inside each step.
- Fresh live submissions are the only reliable end-to-end test.
- Path rules need exact values: capitalization and extra spaces can silently break a match.

## Skills Demonstrated

- No-code workflow automation (triggers, actions, Paths, logging)
- Writing AI prompts that return structured, machine-readable output
- Debugging multi-step automations with run history
- Designing guardrails: human review, confidence thresholds, sensitive-data handling
- Documenting problems and fixes

## What I Would Improve Next

- Split the second path into dedicated Support, KYC, Spam, and Other paths, each with its own action.
- Use a higher confidence threshold for KYC (for example 90%) than for other categories.
- Log in a structured way (one row per submission) and add a daily summary email by category.
- Mask sensitive-looking numbers before anything is stored or logged.
- Add AI-drafted replies for support questions, held for human review before sending.
- Rebuild the same workflow in n8n or Make to compare the platforms.

## Screenshots added

- [ ] The Zap overview showing trigger, AI step, logging, and Paths
- [ ] The AI step output for a Sales message and for an Other message
- [ ] The Google Sheet with logged rows
- [ ] Zap History showing a successful run
- [ ] The email received by the sales inbox (crop or replace real addresses)
