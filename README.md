## Overview: 

Watch the System in Action: https://youtu.be/Bx0eFKGBS3g

Most GTM teams are stuck in Salesforce but frustrated. It's clunky, expensive, and 
hard to work with.

I architected a system that actually works: enriched companies → scored leads → 
imported to Salesforce → automated routing based on business logic → leadership reporting.

**The result:** Data flows in, gets scored, lands in the CRM, automation fires. 
No manual steps. Systems that scale.

## What I Built

### Architecture (Salesforce)

**Custom Objects:**
- `GTM_Campaign` — Tracks individual GTM campaigns (budget, channel, status)
- `Campaign_Activity` — Logs outreach activities (calls, emails, meetings)

**Data Model:**

GTM Campaign (Parent)
├── Campaign_Channel__c (Text) — Which channel (LinkedIn, Cold Email, etc.)
├── Budget__c (Number) — Total budget allocated
├── Status__c (Picklist) — Planned, Active, Completed
└── Total_Activities__c (Roll-Up Summary) — Auto-counts related activities

Campaign Activity (Child)
├── Name (Text) — Activity description
└── GTM_Campaign__c (Master-Detail) — Links to parent campaign


**Key Features:**
- Master-Detail relationships enforce data integrity (no orphaned records)
- Roll-Up Summary auto-counts activities without manual work
- Record-Triggered Flow auto-updates campaign status when activities are logged
- SOQL queries verify data structure and relationships

### Data Pipeline (Clay → Python → Salesforce)

**Step 1: Enrichment (Clay)**
Research 25 target companies with:
- Company size (Startup, SMB, Mid-Market, Enterprise)
- Funding stage (Pre-Seed → Series C+, or Profitable)
- Hiring velocity (Slow, Medium, Fast, Very Fast)

**Step 2: Intelligent Scoring (Python)**
```python
ICP Score = Company Size (0-30) + Funding Stage (0-35) + Hiring Velocity (0-35)
Result: 0-100 scale

Leads >= 45 = Qualified for outreach
```

**Step 3: Import (Salesforce)**
Bulk import 19 scored leads with custom fields:
- `ICP_Score__c` (Number)
- `Company_Size__c` (Picklist)
- `Funding_Stage__c` (Picklist)
- `Hiring_Velocity__c` (Picklist)

**Step 4: Automation (Flow)**
Record-Triggered Flow recognizes high-value leads and auto-routes them 
(Status = "Active") without manual intervention.

## How It Works

1. **Architect the infrastructure** — Custom objects with proper relationships, 
   auto-counting Roll-Up Summaries, and intelligent flows
2. **Enrich external data** — Research companies in Clay with business signals
3. **Score intelligently** — Python script joins data and calculates ICP scores
4. **Import at scale** — Bulk load into Salesforce
5. **Automate routing** — Flow fires automatically, routes based on ICP score
6. **Report on results** — Leadership sees real-time pipeline quality

## Proof of Execution

**SOQL Queries:** Campaign Activities correctly linked to GTM Campaigns  
**Automation:** Campaign status auto-updated from Planned → Active  
**Roll-Up Summary:** Total Activities field auto-counting (no manual entry)  
**Lead Import:** 19 leads successfully imported with ICP scores  

See `/screenshots/` for visual proof.

## Results

- **19 leads** imported into Salesforce
- **100% ICP scoring** — all leads have calculated intelligence
- **Automated routing** — Flow triggers immediately, zero manual work
- **Zero clicks after setup** — System operates independently

## Technical Stack

- **Salesforce:** Custom objects, Master-Detail relationships, Roll-Up Summaries, 
  Record-Triggered Flows, SOQL queries, permission sets
- **Clay:** Company enrichment, signal collection
- **Python:** Data joining, ICP scoring algorithm
- **Google Sheets:** Enriched data storage (template)

## Key Learnings

### Data Architecture Matters
A well-designed schema (Master-Detail relationships, proper field types) 
prevents data corruption and enables automation.

### Workflows > Manual Work
Every repeated task should trigger a workflow. The ROI is immediate: 
status updates, lead routing, notifications — all automatic.

### Intelligence Requires Business Logic
ICP scoring isn't magic. It's: "What does our target customer look like?" 
(big, recently funded, growing fast) translated into a formula.

### Systems Scale, Dashboards Don't
A dashboard that people stare at is a sign of a broken system. 
A working system does the work FOR people — they check the dashboard to verify.

## What's Next (Production Roadmap)

At scale, this system would:
- **Auto-sync from Clay** via API (instead of manual export)
- **Connect to outbound sequences** (high-ICP leads auto-enter cadences)
- **Track performance** (measure: predicted vs. actual conversion)
- **Refine ICP model** (feedback loop: what you scored vs. what converted)
- **Feed into revenue forecasting** (pipeline quality informs quota modeling)

This is systems thinking: understanding data flow, designing for scale, 
automating what's repeatable, building intelligence into the workflow.

GTM infrastructure should move fast and not require manual intervention. 
This system does that.

**Technologies:** Salesforce, Python, Clay, Google Sheets
