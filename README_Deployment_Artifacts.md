# ESWD Production Deployment – Complete Artifact Guide

## Overview

This folder contains everything needed to execute the ESWD Phase 1 production deployment on Wednesday 26 August – Friday 28 August 2026.

**Change Request:** CHG5030730  
**Window:** Wed 26/08 5:00pm – Fri 28/08 5:00pm AEST  
**Deployment Lead:** Ali  
**Deployment Team:** Ali, Jurgen, Chiran, Tom, Roshney (+ Fujitsan standby)

---

## 📋 Artifacts in This Folder

### 1. **ESWD_Deployment_Runsheet_PROD.xlsx** (PRIMARY DOCUMENT) ⭐

**Purpose:** The main execution document. Fill this in real-time during deployment.

**Structure (5 sheets):**

| Sheet | Purpose | When to Use | Owner |
|-------|---------|------------|-------|
| **Pre-Flight Checklist** | 10–15 pre-go-live confirmations | Wed 4:30pm–5:00pm | Ali |
| **Deployment Runsheet** | Main execution tracking (Wed–Fri activities) | During deployment (Wed 5pm–Fri 5pm) | Ali (primary), all team members |
| **First-Run Checks** | Post-deployment validation checklist | Thu 12:30pm–1:15pm | Ali, Jurgen |
| **Rollback Plan** | If deployment fails, follow these steps | Only if needed | Ali |
| **Reference & Sign-Off** | Key docs, participants, approvals | Before & after deployment | All signatories |

**How to Use:**
1. Print or open in Teams/Slack (shared view)
2. Fill in **Status** column in real-time (✓ Pass / ✗ Fail / ⏳ In Progress)
3. Update **Notes/Evidence** column with timestamps and owner names
4. If any step fails, immediately reference the **Rollback Plan** sheet
5. At end of deployment, collect sign-offs on **Reference & Sign-Off** sheet
6. Archive the filled-in runsheet in the CR record

**Key Checkpoints (GO/NO-GO Gates):**
- **Gate 1:** Pre-Flight Checklist (Wed 5:00pm) → Proceed or Defer?
- **Gate 2:** Data Validation (Thu 9:00am) → Ingestion validated?
- **Gate 3:** Deployment & Validation (Thu 5:00pm) → All checks passed?

---

### 2. **RFC_ESWD_Deployment_Runbook_-Prod.docx** (REFERENCE DOCUMENT)

**Purpose:** Detailed guidelines and reference material. Read BEFORE deployment; consult during if needed.

**Sections:**
- **Deployment record** — Jira ticket, environment, operator details
- **Purpose** — What's being deployed and why
- **What is being deployed** — Component list with descriptions
- **Before deployment** — Pre-flight manual steps (Fivetran connection setup, ACL verification)
- **Deployment steps** — Sequential numbered steps (exact notebooks, parameters, SQL commands)
- **First-run checks** — Detailed validation checklist
- **Rollback steps** — If deployment fails, step-by-step undo procedure

**How to Use:**
1. Read through the entire runbook once (Tuesday) to familiarize yourself
2. Keep it open on a second screen during deployment
3. Reference it for:
   - Exact Databricks notebook names and parameters
   - Schema names and table definitions
   - Troubleshooting (if a step fails, check "Rollback steps")
4. **Do NOT** print the entire runbook; focus on the "Deployment steps" section during Wed–Fri

**Relationship to Runsheet:**
- Runbook = "HOW" (detailed technical steps, commands, parameters)
- Runsheet = "WHEN/WHO" (timing, ownership, status tracking)
- **Use together:** Runsheet tells you what to do and who's responsible; runbook tells you how to do it

---

### 3. **Implementation_plan_ESWD_DARE_go-live.xlsx** (DATA REFERENCE)

**Purpose:** Fivetran ingestion schedule and table metadata.

**Sheets:**

| Sheet | Contains | Owner |
|-------|----------|-------|
| **Go-live** | Expanded with Wed–Fri timeline, batch schedule | Ali |
| **Data load summary** | Table names, batch assignments, record counts | Tom/Chiran |

**How to Use:**
1. Reference the **Data load summary** sheet during data validation (Thu 8am)
2. Compare ServiceNow production record counts vs. DARE Prod landing counts
3. Expected deltas are documented (e.g., Engagement records lower because only engagements with actions included)
4. Escalate to Fujitsan if actual counts differ significantly from this sheet

---

### 4. **Deployment_Best_Practices_Guide.md** (REFERENCE)

**Purpose:** Industry best practices for deployments. Strategic reference material.

**Sections:**
1. ITIL Change Management framework
2. Deployment methodologies (Blue-Green, Canary, Big Bang)
3. Deployment phase breakdown (Pre / During / Post)
4. Risk mitigation strategies
5. Communication plan
6. Rollback procedures
7. Lessons learned framework
8. Recommended templates & checklists

**How to Use:**
1. Read Part 1 (ITIL framework) before CAB review
2. Share Part 3 (Deployment phase breakdown) with team
3. Reference Part 4 (Risk mitigation) if issues arise
4. Use Part 7 (Lessons learned) for post-deployment retrospective (Fri afternoon or Mon)
5. Use as training material for future deployments

---

### 5. **Deployment_Template_Comparison.md** (REFERENCE)

**Purpose:** Comparison of deployment template formats. Guidance for customizing templates for future deployments.

**Sections:**
1. Template 1: Traditional ITIL CAB Format
2. Template 2: Timed Runsheet (✓ Recommended for DARE)
3. Template 3: Checklist-Based
4. Template 4: Runbook (Reference)
5. Template 5: Kanban/Task Board
6. Template 6: War Room / Incident Bridge
7. Matrix: Which template for which scenario
8. **Recommendation: Hybrid Approach** (CR + Runsheet + Runbook + Slack + Kanban)

**How to Use:**
1. For ESWD deployment: Use the **Hybrid Approach** (all artifacts already in this folder)
2. For future DARE deployments: Refer to the template matrix to customize your approach
3. Share with stakeholders who ask "why do we need all this documentation?"

---

## 🎯 How to Use These Artifacts During Deployment

### Pre-Deployment (Tuesday 24/08 – Wednesday 4:30pm)

1. **Download & Review**
   - [ ] Download all 5 files
   - [ ] Read runbook (RFC_ESWD_Deployment_Runbook_-Prod.docx) — understand what's being deployed
   - [ ] Review runsheet structure (all 5 sheets)
   - [ ] Share with team; address any questions

2. **Pre-Flight Checklist (Wed 4:30pm)**
   - [ ] Open runsheet, go to "Pre-Flight Checklist" sheet
   - [ ] Complete all 10 items (CAB approval, Fujitsan ACL, CI/CD branch ready, etc.)
   - [ ] Fill in "Completed By / Notes" with owner and timestamp
   - [ ] Make GO/NO-GO decision (if any item fails → DEFER to another window)
   - [ ] Document go/no-go decision at bottom of checklist

### Deployment Execution (Wed 5:00pm – Fri 5:00pm)

1. **During Deployment (Main Runsheet)**
   - [ ] Open runsheet, go to "Deployment Runsheet" sheet
   - [ ] Follow activities in order (Wednesday Ingestion → Thursday Validation → Thursday Deployment → etc.)
   - [ ] For each activity:
     - Update "Status" column: ☐ Pass / ✗ Fail / ⏳ In Progress
     - Fill "Notes / Evidence" with actual timestamp and owner name
     - If step fails, immediately escalate (see "Responsible" column for who to call)

2. **If a Step Fails**
   - [ ] Document the exact error message and step that failed
   - [ ] Consult runbook for troubleshooting
   - [ ] Escalate to responsible person (Fujitsan for Fivetran issues, Jurgen for code issues, etc.)
   - [ ] If unresolvable within 30 min → Escalate to Ali (deployment lead)
   - [ ] If still unresolvable → Reference **Rollback Plan** sheet and execute rollback

3. **After Deployment Steps (First-Run Checks – Thu 12:30pm)**
   - [ ] Open "First-Run Checks" sheet
   - [ ] Run all 12 validation checks in order
   - [ ] Fill "Status" column: ☐ Pass / ✗ Fail
   - [ ] Fill "Evidence / Notes" with command output, query results, screenshots (upload to Slack)
   - [ ] If all pass → Proceed to go/no-go review
   - [ ] If any fail → See runbook "Rollback steps" or escalate

4. **Go/No-Go Review (Thu 5:00pm)**
   - [ ] Deployment Lead (Ali) summarizes: "All checks passed? YES/NO"
   - [ ] **YES** → Proceed to Fri 5pm (FR closure)
   - [ ] **NO** → Decide: attempt fix (Fri contingency window) or rollback?

5. **Contingency Window (Fri 9:00am – 5:00pm)**
   - [ ] Only if Thu checks did NOT all pass
   - [ ] Reference "Rollback Plan" sheet
   - [ ] Follow rollback steps 1–9 in order
   - [ ] Re-run First-Run Checks against rolled-back code
   - [ ] Document result and close CR

### After Deployment (T+1 to T+7 days)

1. **Immediate (Fri 5:00pm)**
   - [ ] Get sign-offs on "Reference & Sign-Off" sheet (deployment lead, CAB rep, Fujitsan)
   - [ ] Archive filled-in runsheet in CR record (CHG5030730)
   - [ ] Send closure notification to stakeholders

2. **Monitoring (Daily for 1 week)**
   - [ ] Monitor job run duration & error rates
   - [ ] Check watermark table for staleness
   - [ ] Review audit logs for anomalies
   - [ ] Confirm Power BI report KPIs correct

3. **Lessons Learned (T+7 days, Friday 2pm)**
   - [ ] Conduct blameless post-mortem (30 min)
   - [ ] Attendees: Ali, Jurgen, Chiran, Tom, Roshney, CAB rep
   - [ ] Document:
     - What went well? (reusable practices)
     - What didn't go well? (process improvements)
     - Data quality check (any bugs post-go-live?)
     - Performance review (cluster sizing adequate?)
   - [ ] Update runbook and runsheet template for next deployment

---

## 📞 Escalation Matrix

| Issue | First Contact | Escalate If Unresolved (10 min) | If Still Unresolved (30 min) |
|-------|---------------|--------------------------------|-------|
| **Fivetran sync stalled** | Ali | Fujitsan rep | Defer to Fri contingency |
| **Databricks job error (code)** | Jurgen | Ali (deployment lead) | Decide: fix or rollback |
| **Data count mismatch** | Chiran + Jurgen | Fujitsan (if source) | Escalate to CAB |
| **Power BI can't connect** | Roshney | Ali (Unity Catalog ACL) | Defer to Fri validation |
| **Cluster performance issue** | Jurgen | Ali (resize cluster) | Defer to Fri contingency |

**Golden Rule:** If you're not sure who to call, call Ali (deployment lead). Ali can pull in Fujitsan, devops, or escalate to CAB if needed.

---

## ✅ Success Criteria

**Deployment is successful if ALL THREE are true by Fri 5:00pm:**

1. ✓ **Pre-Flight Checklist:** All items completed, GO decision made
2. ✓ **Deployment Runsheet:** All activities completed with Status = ☐ Pass
3. ✓ **First-Run Checks:** All 12 validation items passed (Status = ☐ Pass)

**If any of these is NOT true:** Deployment failed → Execute Rollback Plan

---

## 📊 Document Relationship Map

```
                    ┌─────────────────────────────────────────┐
                    │    Change Request (CHG5030730)          │
                    │  Business case, risk, approval          │
                    └────────────────┬────────────────────────┘
                                     │
                    ┌────────────────┴────────────────────┐
                    │  Runsheet (ESWD_Deployment_        │
                    │   Runsheet_PROD.xlsx) ← PRIMARY    │
                    │  When/Who/Status tracking           │
                    │  ├─ Pre-Flight Checklist           │
                    │  ├─ Deployment Runsheet            │
                    │  ├─ First-Run Checks               │
                    │  ├─ Rollback Plan                  │
                    │  └─ Reference & Sign-Off           │
                    └────────────┬───────────────────────┘
                                 │
                    ┌────────────┴──────────────────────┐
                    │  Runbook                          │
                    │  (RFC_ESWD_Deployment_Runbook..) │
                    │  How-to details, parameters       │
                    └──────────────────────────────────┘

                    ┌─────────────────────────────────┐
                    │  Data Reference                 │
                    │  (Implementation_plan_ESWD...)  │
                    │  Fivetran schedule, tables      │
                    └─────────────────────────────────┘

                    ┌─────────────────────────────────┐
                    │  Best Practices Guide           │
                    │  Framework, phases, risks       │
                    │  (Read for context, not exec)   │
                    └─────────────────────────────────┘
```

---

## 🎓 Training & Knowledge Transfer

### For New Team Members

1. Read: **Deployment_Best_Practices_Guide.md** (Part 1–3) — Understand ITIL phases
2. Read: **Deployment_Template_Comparison.md** — Understand why we use a runsheet
3. Review: **ESWD_Deployment_Runsheet_PROD.xlsx** — See example of a filled-in runsheet
4. Read: **RFC_ESWD_Deployment_Runbook_-Prod.docx** — Understand what's being deployed

### For Future Deployments

1. Use **Deployment_Template_Comparison.md** to choose your template
2. Customize **ESWD_Deployment_Runsheet_PROD.xlsx** as a template:
   - Replace project name, dates, timings
   - Replace activity descriptions
   - Replace owner names
3. Create new runbook (copy structure from RFC_ESWD_Deployment_Runbook_-Prod.docx)
4. Share updated templates with team; conduct walkthrough

---

## 📝 Document Ownership & Maintenance

| Document | Owner | Update Frequency | Last Updated |
|----------|-------|------------------|--------------|
| ESWD_Deployment_Runsheet_PROD.xlsx | Ali | Every deployment | 26/08/2026 |
| RFC_ESWD_Deployment_Runbook_-Prod.docx | Jurgen | Every deployment | 26/08/2026 |
| Implementation_plan_ESWD_DARE_go-live.xlsx | Tom/Chiran | As data changes | 26/08/2026 |
| Deployment_Best_Practices_Guide.md | Ali | Annual review | 26/08/2026 |
| Deployment_Template_Comparison.md | Ali | Annual review | 26/08/2026 |

---

## ❓ FAQ

**Q: Do I need all 5 documents?**  
A: For ESWD deployment: YES. Runsheet is primary (execution), runbook is secondary (reference). For simpler future deployments, you might skip the best practices guide or template comparison.

**Q: Should I print these?**  
A: Print the runsheet ONLY (fits on 2 pages). Keep runbook open on a second screen or printed as backup.

**Q: Can I change the runsheet during deployment?**  
A: YES. The runsheet is a living document. Update Status and Notes columns in real-time. Only the Pre-Flight Checklist and First-Run Checks tabs should be "locked" (don't change them mid-deployment).

**Q: What if we find a bug in the runsheet DURING deployment?**  
A: Fix it immediately and make a note in the runsheet. Document the fix in the Slack channel. Update the runsheet template for the next deployment.

**Q: Who should have access to this folder?**  
A: Core team (Ali, Jurgen, Chiran, Tom, Roshney) + CAB representative + Fujitsan. Share via Teams/Slack but keep in a single source of truth (not dozens of email attachments).

**Q: How long should I keep these documents?**  
A: Keep filled-in runsheet for at least 2 years (legal hold). Templates can be updated annually. Archive in the CR record (CHG5030730).

---

## 🚀 Quick Start Checklist

**Before Wed 26/08 5:00pm:**
- [ ] Download all 5 files
- [ ] Read runbook (1 hour)
- [ ] Share runsheet with team
- [ ] Print or open runsheet in shared view
- [ ] Confirm team availability (no conflicts)
- [ ] Test Slack channel (#eswd-deployment)
- [ ] Confirm Fujitsan contact info + availability

**Wed 26/08 4:30pm:**
- [ ] Open runsheet, "Pre-Flight Checklist" sheet
- [ ] Complete 10 items
- [ ] Make GO/NO-GO decision
- [ ] Alert team if proceeding

**Wed 26/08 5:00pm:**
- [ ] Start deployment (first activity on runsheet)
- [ ] Keep runsheet & Slack open for next 48 hours
- [ ] Update status in real-time
- [ ] Follow runbook for technical "how-to" steps

**Fri 28/08 5:00pm:**
- [ ] Collect sign-offs
- [ ] Archive runsheet
- [ ] Schedule lessons learned (Mon or Fri 3pm)

---

## 📞 Support & Questions

- **Deployment Lead:** Ali
- **Technical Issues:** Jurgen (Databricks) or Fujitsan (ServiceNow/Fivetran)
- **Data Issues:** Chiran or Tom
- **Process Questions:** Reference the Deployment_Best_Practices_Guide.md or this README

---

**Good luck! You've got this. 🎯**

---

**Document Version:** 1.0  
**Created:** 26 August 2026  
**Owner:** Ali (DARE Deployment Lead)  
**Last Updated:** 26 August 2026
