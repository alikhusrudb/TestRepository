# Deployment Template Comparison
## Choosing the Right Template for Your Deployment Type

This guide compares common deployment template formats, their strengths, weaknesses, and when to use each for data platform deployments.

---

## Template 1: Traditional ITIL Change Advisory Board (CAB) Format

### Structure
```
Change Request ID:    CHG-2024-XXXX
Title:               Production Deployment – [Project Name]
Requested By:        [Name]
Implementation Date: [Date Range]
Environment:         Production

BUSINESS CASE
- Why: Business justification
- What: Scope of change
- Risk: Risk assessment
- Approval: CAB sign-off

IMPLEMENTATION PLAN
- Pre-deployment checklist (yes/no)
- Deployment steps (1, 2, 3...)
- Post-deployment validation
- Rollback procedure

APPROVALS & SIGN-OFF
- [Name] ___________ Date: ___
- [Name] ___________ Date: ___
```

### Strengths
- ✓ Formal, audit-friendly
- ✓ Clear approval chain
- ✓ Comprehensive risk documentation
- ✓ Suitable for regulated environments (HIPAA, SOX)

### Weaknesses
- ✗ Heavy, time-consuming to fill out
- ✗ Not ideal for real-time tracking during deployment
- ✗ Doesn't capture actual execution flow
- ✗ Easy to become outdated

### When to Use
- **Enterprise governance-heavy environments**
- **Regulated industries** (healthcare, finance)
- **Major infrastructure changes**
- **Cross-department deployments**

### For ESWD?
**Recommended:** Use this for the formal CR approval process (CHG5030730), but combine with Runsheet (Template 2) for execution.

---

## Template 2: Timed Runsheet (Execution-Focused) ← **RECOMMENDED FOR DARE**

### Structure
```
Deployment Runsheet – [Project] – [Date Range]
Change: CHG5030730 | Environment: DARE Prod | Window: Wed 26/08 5pm – Fri 28/08 5pm

Time Start | Time End | Activity | Owner | Status | Notes
-----------|----------|----------|-------|--------|-------
4:30pm     | 5:00pm   | Pre-flight checklist | Ali | ☐ Pass | [Notes]
5:00pm     | 5:30pm   | Create Fivetran connection | Ali | ☐ Pass | [Notes]
[etc...]
```

### Strengths
- ✓ Easy to follow during execution
- ✓ Real-time status tracking (✓/✗/⏳)
- ✓ Clear owner for each task
- ✓ Built-in notes field for evidence
- ✓ Minimal overhead; quick to update
- ✓ Excellent for team coordination

### Weaknesses
- ✗ Doesn't capture full risk/business context (need CR for that)
- ✗ Hard to reference detailed "how-to" steps (need runbook)
- ✗ Time estimates might slip (need buffer columns)
- ✗ Not suitable as formal audit record alone

### When to Use
- **Data platform deployments** (ETL, data warehouse, analytics)
- **Agile/DevOps teams** (prefer execution speed)
- **Scheduled maintenance windows**
- **Small-to-medium scope changes**

### For ESWD?
**Highly Recommended:** This is the primary execution document. Filled in real-time during Wed–Fri window.

---

## Template 3: Checklist-Based (Validation-Focused)

### Structure
```
PRE-DEPLOYMENT CHECKLIST
☐ Item 1 – Description
☐ Item 2 – Description
[etc...]
☐ GO/NO-GO: Proceed or Defer?

DEPLOYMENT CHECKLIST
☐ Deploy Step 1
☐ Deploy Step 2
[etc...]

POST-DEPLOYMENT VALIDATION
☐ Check 1 – Pass/Fail
☐ Check 2 – Pass/Fail
[etc...]
☐ All checks passed? YES / NO
```

### Strengths
- ✓ Simple, low friction
- ✓ Clear pass/fail gates
- ✓ Good for smaller deployments
- ✓ Easy to scan visually
- ✓ Works well for non-technical stakeholders

### Weaknesses
- ✗ No timing information (when should each happen?)
- ✗ No owner accountability (who does each?)
- ✗ No sequential dependency tracking
- ✗ Hard to coordinate across teams

### When to Use
- **Simple, single-step deployments**
- **Pre-flight & post-deployment validation**
- **Non-technical teams** (business users)
- **Embedded within larger documents**

### For ESWD?
**Recommended:** Use for Pre-Flight Checklist (Wed 4:30pm) and First-Run Checks (Thu 12:30pm) tabs within the broader runsheet.

---

## Template 4: Runbook (Reference Document, NOT Execution)

### Structure
```
RUNBOOK: [Project Name]

1. PURPOSE
   What's being deployed and why

2. COMPONENTS
   - Component A: Description
   - Component B: Description

3. BEFORE DEPLOYMENT
   Step 1: Do X
   Step 2: Do Y

4. DEPLOYMENT STEPS (Sequential)
   Step 1: Run notebook X with parameters Y
   Step 2: Run job Z
   Step 3: Execute SQL query ABC
   [etc...]

5. FIRST-RUN CHECKS
   Check 1: Verify table X exists
   Check 2: Validate row counts

6. ROLLBACK STEPS
   If step N fails, execute rollback step A
   [etc...]

7. TROUBLESHOOTING
   Issue: X happened. Solution: Do Y.
```

### Strengths
- ✓ Detailed reference material
- ✓ Ideal for training new team members
- ✓ Good for post-mortem documentation
- ✓ Covers "how-to" details (parameters, commands)
- ✓ Can be reused for future deployments

### Weaknesses
- ✗ Not suitable for real-time execution tracking
- ✗ Static (hard to update during deployment)
- ✗ Can become lengthy and hard to navigate
- ✗ No ownership/accountability built in

### When to Use
- **Operational reference documentation**
- **Training materials**
- **Regulatory/audit requirements**
- **First-time deployments** (needs detailed guidance)

### For ESWD?
**Recommended:** Use as the RFC_ESWD_Deployment_Runbook_-Prod.docx (reference/guidelines). Keep separate from runsheet for clarity.

---

## Template 5: Kanban/Task Board (Agile Style)

### Structure (Trello, Jira, Azure DevOps)
```
TO DO              | IN PROGRESS        | DONE
───────────────────────────────────────────────
Card 1: Pre-flight | Card 5: Fivetran   | ✓ Card 2
description        | sync (Thu 8am)      | ✓ Card 3
assignee: Ali      | assignee: Ali       | ✓ Card 4
due: Wed 4pm       | progress: 70%       |

Card 6: Deploy     | Card 7: Power BI    |
scripts            | validation          |
assignee: Jurgen   | assignee: Tom       |
due: Thu 9am       | due: Thu 2pm        |
```

### Strengths
- ✓ Visual status tracking
- ✓ Good for distributed teams
- ✓ Integrates with dev tools (Jira, GitHub)
- ✓ Real-time collaboration
- ✓ Built-in notifications

### Weaknesses
- ✗ Requires tool access during deployment (Wi-Fi, VPN)
- ✗ No formal sign-off capability
- ✗ Hard to audit or print for formal records
- ✗ Can become cluttered with details

### When to Use
- **DevOps/Agile teams** using Jira/Azure DevOps already
- **Distributed teams** in different time zones
- **Continuous deployments** (multiple per week)
- **High-velocity teams** wanting real-time dashboards

### For ESWD?
**Optional supplement:** Could use Jira subtasks alongside runsheet, but runsheet is primary for DARE.

---

## Template 6: War Room / Incident Bridge Format

### Structure (During Active Deployment)
```
=== ESWD PRODUCTION DEPLOYMENT – LIVE BRIDGE ===

DATE: 26-28 August 2026
BRIDGE: Slack #eswd-deployment (or Teams channel)
LEAD: Ali
BRIDGE STATUS: ◀ LIVE ▶

=== CURRENT STATUS ===
Current Phase: [Wed Ingestion / Thu Validation / etc.]
Last Update: 2026-08-27 09:15am (15 min ago)

=== BLOCKERS ===
🔴 BLOCKER #1: Fivetran batch 3 sync running slow
   Owner: Ali | Escalation: Fujitsan | ETA Resolution: 1 hour

=== TEAM STATUS ===
Ali (Lead): Investigating Fivetran logs
Jurgen (Dev): Ready for deployment step 1
Chiran (QA): Monitoring dashboard
Tom (BA): On standby

=== NEXT STEPS ===
1. Resolve Fivetran batch 3 (Ali, ETA 10:30am)
2. Run go/no-go checkpoint (Ali, scheduled 10:45am)
3. Proceed to CI/CD deployment (Jurgen, if checkpoint passes)
```

### Strengths
- ✓ Excellent for large, high-stakes deployments
- ✓ Real-time communication
- ✓ Blocker visibility to entire team
- ✓ Good for incident management

### Weaknesses
- ✗ Requires active facilitation (bridge moderator)
- ✗ Chat can be noisy and hard to search
- ✗ Hard to use as formal record (need transcript)
- ✗ Overkill for small/simple deployments

### When to Use
- **24-hour deployments** with on-call rotation
- **High-risk deployments** with large blast radius
- **Multi-team coordination** (DevOps, DBA, Network)
- **Incident response** if deployment goes wrong

### For ESWD?
**Recommended as supplement:** Use Slack channel for real-time updates, but runsheet is the formal record. Transcribe key blocker/resolution info back into runsheet notes.

---

## Template Recommendation Matrix

| Scenario | Primary Template | Supplement | Runbook |
|----------|------------------|------------|---------|
| **Small, low-risk deployment** | Checklist | — | Optional |
| **Medium, scheduled maintenance** | **Runsheet** | Slack updates | Yes |
| **Large, complex, multi-team** | **Runsheet** + Kanban | War room bridge | Yes |
| **Regulated environment (SOX, HIPAA)** | CAB CR + Runsheet | Kanban (audit trail) | Yes (comprehensive) |
| **Agile/DevOps (frequent deploys)** | Kanban | Runsheet (for releases) | Thin |
| **Data platform deployment** (DARE/ESWD) | **Runsheet** ← Recommended | Checklist (pre/post) | Yes |

---

## RECOMMENDED FOR DARE: Hybrid Approach

**Use ALL FIVE in combination:**

1. **CAB CR** (CHG5030730) = Formal business context & approval
2. **Runsheet** (ESWD_Deployment_Runsheet_PROD.xlsx) = **PRIMARY EXECUTION DOCUMENT**
   - Includes Pre-Flight Checklist (Sheet 1)
   - Includes Deployment Runsheet (Sheet 2)
   - Includes First-Run Checks (Sheet 3)
   - Includes Rollback Plan (Sheet 4)
3. **Runbook** (RFC_ESWD_Deployment_Runbook_-Prod.docx) = Reference details (how-to steps, schema definitions, troubleshooting)
4. **Slack Channel** (#eswd-deployment) = Real-time team communication & blocker tracking
5. **Post-Deployment Kanban** (Jira) = Lessons learned, action items, improvements for next deployment

**Why This Works:**
- CR = Governance (compliance, approval)
- Runsheet = Execution (who, what, when, status)
- Runbook = Details (how, why, troubleshooting)
- Slack = Communication (real-time, informal)
- Kanban = Improvement (learning, backlog)

---

## Quick Template Selection Guide

**Ask yourself:**

1. **What's my team size?**
   - 1–3 people → Simple Checklist
   - 4–6 people → Runsheet (recommended)
   - 7+ people → Runsheet + Kanban + War Room

2. **What's my risk level?**
   - Low risk → Checklist
   - Medium risk → Runsheet + Runbook
   - High risk / Regulated → CAB CR + Runsheet + Kanban + War Room

3. **How long is the deployment?**
   - < 2 hours → Checklist
   - 2–8 hours → Runsheet
   - 24+ hours → Runsheet + Kanban + War Room + on-call rotation

4. **Do I have formal governance requirements?**
   - No → Runsheet (enough)
   - Yes → CAB CR + Runsheet + audit trail

**For ESWD Deployment:** You have medium risk, 6-person team, 48-hour window, and formal governance needs. **Use the Hybrid Approach (above).**

---

## Implementation Checklist for DARE Deployments

Use this checklist when planning your next deployment:

### Before You Start

- [ ] Define scope (what's changing?)
- [ ] Identify risk level (high/medium/low)
- [ ] List team members (who's involved?)
- [ ] Estimate duration (how long?)
- [ ] Check governance requirements (CAB needed?)

### Choose Templates

- [ ] **Primary:** Runsheet (always recommended for DARE)
- [ ] **Plus:** Runbook (if complex or new)
- [ ] **Plus:** CAB CR (if governance required)
- [ ] **Plus:** Kanban (if 7+ people)
- [ ] **Plus:** War room bridge (if high risk or 24+ hours)

### Create Runsheet Structure

- [ ] Sheet 1: Pre-Flight Checklist (10–15 items, go/no-go gate)
- [ ] Sheet 2: Deployment Runsheet (Time Start | End | Activity | Owner | Status | Notes)
- [ ] Sheet 3: First-Run Checks (post-deployment validation)
- [ ] Sheet 4: Rollback Plan (if deployment fails)
- [ ] Sheet 5: Reference & Sign-Off (links, participants, approvals)

### Create Runbook Sections (if needed)

- [ ] Purpose
- [ ] Components being deployed
- [ ] Before deployment (manual prep steps)
- [ ] Deployment steps (sequential, numbered)
- [ ] First-run checks (validation details)
- [ ] Rollback steps
- [ ] Troubleshooting guide

### Before Deployment

- [ ] Runsheet shared with all participants
- [ ] Roles & escalation matrix clear
- [ ] Backup communication channel ready (Slack, Teams)
- [ ] Previous version backups archived
- [ ] Staging deployment successful

### During Deployment

- [ ] Fill in runsheet real-time (status, owner, notes)
- [ ] Post blockers to Slack immediately
- [ ] Use go/no-go gates to make decisions
- [ ] Document all escalations

### After Deployment

- [ ] Obtain sign-offs (deployment lead, CAB, Fujitsan)
- [ ] Archive filled runsheet in CR record
- [ ] Conduct lessons learned (T+7 days)
- [ ] Update templates for next deployment

---

## Conclusion

**There's no one-size-fits-all template.** The best approach combines:

1. **Formal governance** (CAB CR) for approval & audit
2. **Execution readiness** (Runsheet) for team coordination
3. **Operational reference** (Runbook) for how-to details
4. **Real-time communication** (Slack/Teams) for incident management
5. **Continuous improvement** (Kanban/Retrospective) for learning

**For DARE/ESWD:** Use the **Runsheet + Runbook + Slack** hybrid. It gives you the best of both worlds: formal structure (runsheet) + operational flexibility (Slack updates) + detailed reference (runbook).

**Recommended Next Steps:**
1. Archive this comparison guide for future reference
2. Customize the runsheet template for each deployment
3. Update your CR template to reference this guide
4. Share with your team and gather feedback

---

**Document Version:** 1.0  
**Last Updated:** 26 August 2026  
**Owner:** Ali (DARE Deployment Lead)  
**Next Review:** Post-ESWD deployment (28 August 2026)
