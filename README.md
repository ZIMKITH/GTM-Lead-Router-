# GTM Lead Router — Automated Lead Enrichment & CRM Enrollment Pipeline

> *Built by Zimkitha Ntshikaniso | GTM Engineer & AI Automation Specialist*

![n8n](https://img.shields.io/badge/n8n-Workflow_Automation-orange?style=for-the-badge&logo=n8n)
![Clay](https://img.shields.io/badge/Clay-Lead_Enrichment-purple?style=for-the-badge)
![HubSpot](https://img.shields.io/badge/HubSpot-CRM_Integration-ff7a59?style=for-the-badge&logo=hubspot)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-Database-34a853?style=for-the-badge&logo=googlesheets)
![Apollo](https://img.shields.io/badge/Apollo.io-Lead_Sourcing-blue?style=for-the-badge)
![Reoon](https://img.shields.io/badge/Reoon-Email_Verification-red?style=for-the-badge)
![Slack](https://img.shields.io/badge/Slack-Real_Time_Alerts-4a154b?style=for-the-badge&logo=slack)
![Status](https://img.shields.io/badge/Status-Production_Ready-success?style=for-the-badge)

---

## What This System Solves

Most B2B sales teams lose deals before they even start. Bad data, slow routing, and manual CRM entry mean leads sit in a spreadsheet while your competitors are already in their inbox.

The **GTM Lead Router** is a fully automated, 20-node pipeline that takes raw leads from Apollo, enriches them through Clay, verifies every email through Reoon, scores and tiers each contact by revenue potential, then automatically routes them into HubSpot, fires real-time Slack alerts, and logs every single action. 

Zero manual input. Zero guessing. **Your sales team wakes up to a CRM that's already been working for hours.**

|
 The Revenue Killer 
|
 What It Costs You 
|
 What This System Does 

Poor data hygiene
|
 Bounced emails. Burned domains. Wasted sequences. 
|
Reoon email firewall
 verifies every contact before they touch a campaign. 
|
Slow speed-to-lead
|
 Leads go cold in under 5 mins. Manual entry takes hours. 
|
Automated routing
 fires the moment a lead is enriched and tiered. 
|

## System Architecture

```text
[ Apollo.io ] 
     ↓ (Raw Leads)
[ Clay ] → AI Scoring & Waterfall Enrichment
     ↓
[ Reoon ] → Email Verification Firewall
     ↓
[ Google Sheets ] → Database (Raw & Enriched)
     ↓
[ n8n Engine ] → 20-Node Switch/Routing Logic
     ├── HOT BRANCH  → Slack Alert + HubSpot POST
     ├── WARM BRANCH → Slack Alert + HubSpot POST
     └── COLD BRANCH → Hold & Log
The Full Journey Of A Lead
SOURCED: Apollo.io pulls ICP-matched leads by title, company size, industry, and seniority.
ENRICHED: Clay runs waterfall enrichment (AI classification, lead scoring 0-100, LinkedIn URL resolution, email quality grading).
VERIFIED: Reoon email firewall ensures no unverified email touches a sending domain.
ROUTED: n8n Switch node routes by Lead Tier (Hot, Warm, Cold).
ENROLLED: HubSpot contact created or updated via API (status set to IN_PROGRESS).
LOGGED: Every action is written to the System Log.
PROTECTED: Global Error Handler monitors the pipeline and routes failures to #system-errors in Slack.
Tech Stack
Layer	Tool	Purpose
Lead Sourcing	Apollo.io	ICP-matched lead lists
Enrichment	Clay	Waterfall enrichment, AI scoring, URL resolution
Email Verification	Reoon	Email firewall — domain protection
Automation Engine	n8n	20-node self-hosted workflow orchestration
CRM	HubSpot	Contact enrollment via Private App API
Alerts	Slack	Real-time lead routing notifications
Database	Google Sheets	4-tab architecture (raw, enriched, campaign, log)
Database Architecture (Google Sheets)
The system relies on a strict 4-tab architecture to maintain full state and prevent double-processing.

View Tab 1: Raw Leads (Intake Layer)
View Tab 2: Enriched Leads (Intelligence Layer)
View Tab 3 & 4: Campaign Tracking & System Log (Audit Layer)
n8n Workflow — The Automation Engine
Main Pipeline:

Node 1-2: Schedule Trigger & Get Rows (Pulls pending leads).
Node 3: Filter Node (Sent To Automation ≠ Yes) prevents duplicate processing.
Node 4: Switch Node routes by Lead_Tier.
Routing Logic:

🔥 Hot Branch (Nodes 5-9): Triggers #hot-leads Slack alert → HTTP batch/upsert to HubSpot (Priority) → Updates Sheet to Processed → Appends Log.
☀️ Warm Branch (Nodes 10-14): Triggers #warm-leads Slack alert → HTTP batch/upsert to HubSpot (Nurture) → Updates Sheet to Processed → Appends Log.
🧊 Cold Branch (Nodes 15-17): Flags in Sheet for future sequencing → Appends Log.
System Protection:

Error Handler (Nodes 18-20): Catches any failure → Writes Failed to Pipeline Status → Sends instant Slack alert with error payload.
HubSpot API Integration (Batch Upsert)
Instead of basic POST requests, this system uses HubSpot's batch/upsert endpoint to ensure database integrity and eliminate duplicate records.

Authentication: Private App Token
Endpoint: POST /crm/v3/objects/contacts/batch/upsert
Deduplication Key: email

json
{
  "inputs": [
    {
      "idProperty": "email",
      "id": "contact@company.com",
      "properties": {
        "firstname": "={{ $json['First Name'] }}",
        "lastname": "={{ $json['Last Name'] }}",
        "email": "={{ $json['Email'] }}",
        "company": "={{ $json['Company Name'] }}",
        "hs_linkedin_url": "={{ $json['Linkedin URL'] }}",
        "hs_lead_status": "IN_PROGRESS",
        "lead_tier": "={{ $json['Lead Tier'] }}"
      }
    }
  ]
}
Scenario	System Behavior
Contact does not exist	Creates new contact
Contact already exists	Updates existing contact
Duplicate email detected	Handled natively (No conflict errors)
Malformed/Missing email	Blocked prior to HubSpot API call
The "Glass Box" Philosophy
This system is built on one non-negotiable principle: AI never has unchecked write-access to a database.

Every input, logic node, and output is:

Auditable — The System Log records every action.
Traceable — Lead IDs thread through every tab.
Reversible — Pipeline Status flags every state.
Protected — The Error Handler catches every failure.
No black boxes. No silent failures. No hallucinated data.

