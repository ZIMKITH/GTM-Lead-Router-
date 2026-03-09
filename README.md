# GTM Lead Router — Automated Lead Enrichment & CRM Enrollment Pipeline

> *Built by Zimkitha Ntshikaniso | GTM Engineer & AI Automation Specialist*

---

![n8n](https://img.shields.io/badge/n8n-Workflow_Automation-orange?style=for-the-badge&logo=n8n)
![Clay](https://img.shields.io/badge/Clay-Lead_Enrichment-purple?style=for-the-badge)
![HubSpot](https://img.shields.io/badge/HubSpot-CRM_Integration-ff7a59?style=for-the-badge&logo=hubspot)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-Database-34a853?style=for-the-badge&logo=googlesheets)
![Apollo](https://img.shields.io/badge/Apollo.io-Lead_Sourcing-blue?style=for-the-badge)
![Reoon](https://img.shields.io/badge/Reoon-Email_Verification-red?style=for-the-badge)
![Slack](https://img.shields.io/badge/Slack-Real_Time_Alerts-4a154b?style=for-the-badge&logo=slack)
![Smartlead](https://img.shields.io/badge/Smartlead-Outbound_Sending-1a73e8?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Production_Ready-success?style=for-the-badge)
![Nodes](https://img.shields.io/badge/Workflow_Nodes-20-brightgreen?style=for-the-badge)
![Architecture](https://img.shields.io/badge/Architecture-Glass_Box-lightgrey?style=for-the-badge)
![Location](https://img.shields.io/badge/Built_In-Johannesburg_ZA-gold?style=for-the-badge)

---

## What This System Does

Most B2B sales teams lose deals before they even start.

Bad data. Slow routing. Manual CRM entry. Leads sitting in a 
spreadsheet while your competitors are already in their inbox.

This system eliminates all of that.

The **GTM Lead Router** is a fully automated, 20-node pipeline 
that takes raw leads from Apollo, enriches them through Clay, 
verifies every email through Reoon, scores and tiers each contact 
by revenue potential — then automatically routes them into HubSpot, 
fires real-time Slack alerts, and logs every single action.

Zero manual input. Zero guessing.

**Your sales team wakes up to a CRM that's already been working 
for hours.**

---

## 💀 The Two Revenue Killers This System Eliminates

|
 Problem 
|
 What It Costs You 
|
 What This System Does 
|
|
---
|
---
|
---
|
|
 Poor data hygiene 
|
 Bounced emails. Burned domains. Wasted sequences. 
|
 Reoon email firewall verifies every contact before they touch a campaign 
|
|
 Slow speed-to-lead 
|
 Leads go cold in under 5 minutes. Manual entry takes hours. 
|
 Automated routing fires the moment a lead is enriched and tiered 
|

---

## Full System Architecture
Apollo.io → Clay → Google Sheets → n8n → HubSpot
↓
Slack
↓
System Log (Sheets)

text

### The Full Journey Of A Lead
SOURCED — Apollo.io pulls ICP-matched leads by title,
company size, industry, and seniority

ENRICHED — Clay runs waterfall enrichment:
AI classification, lead scoring (0-100),
LinkedIn URL resolution, email quality grading

VERIFIED — Reoon email verification firewall:
No unverified email touches a sending domain

SCORED — Every lead receives:
→ Lead Score (0-100)
→ Lead Tier (Hot / Warm / Cold)
→ Email Quality (Business / Personal / Catch-All)

ROUTED — n8n Switch node routes by Lead Tier:
→ Hot = Slack alert + HubSpot enrollment (priority)
→ Warm = Slack alert + HubSpot enrollment (nurture)
→ Cold = Logged + held for future sequencing

ENROLLED — HubSpot contact created or updated via API:
All fields populated. Lead tier stamped.
Status set to IN_PROGRESS.

LOGGED — Every action written to System Log:
Timestamp. Event Type. Lead ID. Status. Notes.

PROTECTED — Error Handler monitors entire pipeline:
Any failure → Pipeline Status = Failed
Instant Slack alert to #system-errors

text

---

## 🛠️ Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| Lead Sourcing | Apollo.io | ICP-matched lead lists |
| Enrichment | Clay | Waterfall enrichment, AI scoring, URL resolution |
| Email Verification | Reoon | Email firewall — domain protection |
| Automation Engine | n8n (self-hosted) | 20-node workflow orchestration |
| CRM | HubSpot | Contact enrollment via Private App API |
| Alerts | Slack | Real-time lead routing notifications |
| Database | Google Sheets | 4-tab architecture — raw, enriched, campaign, log |
| Sending Infrastructure | Smartlead / Instantly | Cold outbound sequences |

---

## 📊 Google Sheets Architecture — 4 Tabs

### TAB 1 — Raw Leads
*The intake layer. Every lead enters here first.*

| Column | Description |
|---|---|
| Lead ID | Unique identifier — generated in Clay |
| First Name | Contact first name |
| Last Name | Contact last name |
| Seniority Level | VP / Director / C-Suite etc |
| Company Name | Target company |
| Email | Raw email from Apollo |
| Email Status | Verification status from Reoon |
| Company Size | Employee headcount |
| Industry | Industry classification |
| Company Domain | Root domain |
| LinkedIn URL | Profile or company URL |
| Country | Contact location |
| Pipeline Status | Pending / Processed / Failed |

---

### TAB 2 — Enriched Leads
*The intelligence layer. Clay outputs live here.*

| Column | Description |
|---|---|
| Lead ID | Matched to Raw Leads |
| First Name | Contact first name |
| Last Name | Contact last name |
| Title | Job title |
| Company Name | Target company |
| Email | Verified email |
| Email Verified | Boolean — Yes / No |
| Lead Tier | Hot / Warm / Cold |
| Lead Score | 0-100 — AI generated in Clay |
| Sent To Automation | Yes / No — prevents duplicate processing |
| Seniority Level | Seniority classification |
| Company Size | Employee headcount |
| Email Quality | Business / Personal / Catch-All |
| Industry | Industry classification |
| LinkedIn URL | Resolved profile URL |
| Company Domain | Root domain |
| Country | Contact location |
| Date Enriched | ISO timestamp |

---

### TAB 3 — Campaign Tracking
*The outbound layer. Every sequence logged here.*

| Column | Description |
|---|---|
| Lead ID | Matched identifier |
| Email | Contact email |
| First Name | First name |
| Company Name | Company |
| Lead Tier | Hot / Warm / Cold |
| Campaign Name | Active sequence name |
| Status | Active / Paused / Completed |
| Date Added To Campaign | Timestamp |
| Email Sent | Boolean |
| Opened | Boolean |
| Replied | Boolean |
| Reply Sentiment | Positive / Neutral / Negative |

---

### TAB 4 — System Log
*The audit layer. Every action recorded.*

| Column | Description |
|---|---|
| Timestamp | Exact datetime of action |
| Event Type | Lead Enrolled / Error / Updated |
| Lead ID | Which lead triggered the event |
| Lead Email | Contact email |
| Action Taken | What the system did |
| Status | Success / Failed |
| Notes | Error messages or additional context |

---

## n8n Workflow — 20 Nodes

### Main Pipeline
NODE 1 — Schedule Trigger
Fires workflow on defined schedule

NODE 2 — Get Rows (Enriched Leads)
Pulls all rows from Enriched Leads tab

NODE 3 — Filter Node
Condition: Sent To Automation ≠ Yes
Prevents any lead from being processed twice

NODE 4 — Switch Node
Routes by Lead Tier:
→ Hot → Nodes 5-9
→ Warm → Nodes 10-14
→ Cold → Nodes 15-17


### Hot Branch — Priority Leads
NODE 5 — Slack Alert (Hot)
Fires to #hot-leads channel
Includes: Name, Company, Title, Score

NODE 6 — HTTP Request — HubSpot Upsert (Hot)
Method: POST
Endpoint: /crm/v3/objects/contacts/batch/upsert
Creates or updates contact
Stamps lead_tier = Hot

NODE 7 — Update Row — Enriched Leads (Hot)
Writes: Sent To Automation = Yes

NODE 8 — Update Row — Raw Leads (Hot)
Writes: Pipeline Status = Processed

NODE 9 — Append Row — System Log (Hot)
Records full action with timestamp


### Warm Branch — Nurture Leads
NODE 10 — Slack Alert (Warm)
Fires to #warm-leads channel

NODE 11 — HTTP Request — HubSpot Upsert (Warm)
Method: POST
Endpoint: /crm/v3/objects/contacts/batch/upsert
Creates or updates contact
Stamps lead_tier = Warm

NODE 12 — Update Row — Enriched Leads (Warm)
Writes: Sent To Automation = Yes

NODE 13 — Update Row — Raw Leads (Warm)
Writes: Pipeline Status = Processed

NODE 14 — Append Row — System Log (Warm)
Records full action with timestamp


### Cold Branch — Hold For Later
NODE 15 — Update Row — Enriched Leads (Cold)
Flagged for future sequencing

NODE 16 — Update Row — Raw Leads (Cold)
Writes: Pipeline Status = Processed

NODE 17 — Append Row — System Log (Cold)
Records full action with timestamp


### Error Handler — System Protection
NODE 18 — Error Trigger
Catches any failure in the pipeline

NODE 19 — Update Row — Raw Leads
Writes: Pipeline Status = Failed

NODE 20 — Slack Alert — #system-errors
Instant notification with error details


---

## HubSpot API Integration

**Authentication:** Private App Token
**Endpoint:** `POST /crm/v3/objects/contacts/batch/upsert`
**ID Property:** Email (deduplication key)

**Fields Written To HubSpot:**

```json
{
  "inputs": [
    {
      "idProperty": "email",
      "id": "contact@company.com",
      "properties": {
        "firstname": "First Name",
        "lastname": "Last Name",
        "email": "contact@company.com",
        "company": "Company Name",
        "hs_linkedin_url": "LinkedIn URL",
        "hs_lead_status": "IN_PROGRESS",
        "lead_tier": "Hot | Warm | Cold"
      }
    }
  ]
}
Upsert Logic:

Scenario	Behavior
Contact does not exist	Creates new contact
Contact already exists	Updates existing contact
Duplicate email detected Never throws conflict error
Empty or malformed email	Blocked before reaching HubSpot

Glass Box Architecture
This system is built on one non-negotiable principle:

AI never has unchecked write-access to a database.

Every input, every logic node, and every output is:


Auditable   — System Log records every action
Traceable   — Lead ID threads through every tab
Reversible  — Pipeline Status flags every state
Protected   — Error Handler catches every failure
No black boxes. No silent failures. No mystery data.

Business Value
Metric	Manual Process	This System
Time to enroll a lead in CRM	3-5 minutes per lead	Seconds — automated
Data entry errors	High — human input	Zero — API mapped
Duplicate contacts	Common	Impossible — upsert logic
Lead routing speed	Hours	Real-time Slack alert
Pipeline visibility	Low	Full audit trail
Domain protection	Reactive	Proactive — Reoon firewall
Sales team readiness	Delayed	CRM ready before 9am standup



Built By
Zimkitha Ntshikaniso
GTM Engineer & AI Automation Specialist
Johannesburg, South Africa → US & UK Remote Teams

I build the infrastructure that makes sure your sales team
is always talking to the right people, with the right
information, at the right time automatically.
No manual data entry. No bounced emails. No guessing.

