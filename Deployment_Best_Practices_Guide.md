# Deployment Best Practices for Data Platforms
## Industry Standards & Change Management Framework

**Document Purpose:** Guidance for planning, executing, and validating production deployments in enterprise data environments (data warehouses, data lakes, analytics platforms).

**Best applicable to:** DARE, enterprise Databricks, cloud data platforms, ETL/ELT pipelines, analytics infrastructure.

---

## Part 1: Industry Frameworks & Standards

### 1.1 ITIL Change Management (IT Infrastructure Library)

ITIL v3/v4 defines a structured approach to managing changes in IT operations:

| Phase | Purpose | Key Activities |
|-------|---------|-----------------|
| **Assess** | Evaluate change impact, risk, and dependencies | Document scope, business case, risk matrix, stakeholders |
| **Build** | Develop and test the change in non-prod environments | Dev → Test → Staging cycles; unit/integration/acceptance tests |
| **Authorize** | Obtain approval from Change Advisory Board (CAB) | CAB review, risk approval, scheduling |
| **Deploy** | Execute the change in production during a defined window | Runsheet execution, monitoring, rollback readiness |
| **Validate** | Confirm the change achieved its business objectives | Post-deployment testing, user acceptance, performance checks |
| **Communicate** | Notify stakeholders of status throughout the process | Status updates, incident escalation, closure documentation |

**For ESWD Deployment:**
- **Assess**: CHG5030730 risk assessment, impact on DARE users, rollback scenarios
- **Build**: Staging deployment (Phase 0), tested before Prod go-live
- **Authorize**: CAB approval before Wed 26/08 5:00pm
- **Deploy**: Runsheet execution Wed–Fri, monitored by deployment lead (Ali)
- **Validate**: First-run checks, Power BI sense-check
- **Communicate**: Status updates to stakeholders, CR closure notification

### 1.2 The "Three Gates" of Production Readiness

Industry best practice gates before any production deployment:

**Gate 1: "Build Gate"** (Pre-flight checklist)
- All code reviewed and merged to main branch
- All automated tests passing (unit, integration, schema)
- Runbook available and tested in Staging
- Stakeholders briefed; no open questions
- Rollback plan documented and tested

**Gate 2: "Release Gate"** (Go/no-go checkpoint)
- CAB approval received
- Environment stable; no concurrent maintenance windows
- All participants confirmed and available
- Monitoring/alerting configured
- Previous version backups archived and accessible

**Gate 3: "Validation Gate"** (Post-deployment)
- First-run checks 100% passed
- Data validation against source complete (count reconciliation, key uniqueness)
- User acceptance tests passed (Power BI reporting)
- No open data issues or performance degradation
- Change closure documented and filed

**In ESWD terms:**
- **Gate 1**: Pre-Flight Checklist (Wed 4:30pm)
- **Gate 2**: Go/no-go Checkpoint (Thu 9:00am)
- **Gate 3**: Go/no-go Review (Thu 5:00pm)

---

## Part 2: Deployment Methodologies

### 2.1 **Blue-Green Deployment** (Best for Data Platforms)

Maintain two identical production environments; switch traffic between them.

```
BLUE (Current)          GREEN (New)
└─ DARE Prod (old)  ← → DARE Prod (with ESWD loaded)
   Live until switch    Tested, ready to activate
```

**Pros:**
- Instant rollback (flip the switch back)
- Zero downtime
- No parallel runs or hybrid states

**Cons:**
- Double infrastructure cost
- Data sync complexity for stateful systems

**DARE Context:** Not feasible for a data lake (stateful Delta tables). Instead, use "Canary Deployment" (see below).

### 2.2 **Canary Deployment** (Recommended for Databricks/Delta)

Gradually roll out to a small subset of tables/users first; then expand.

```
Phase 1: Deploy to Staging (small test subset)
         ↓ Validate & Approve
Phase 2: Deploy to Prod curated layer (tables created, no data yet)
         ↓ Ingest ServiceNow Prod data via Fivetran
Phase 3: Load curated tables from staging → curated
         ↓ First-run checks pass
Phase 4: Provision Power BI access; expand to all users
```

**Pros:**
- Staged rollout reduces blast radius
- Early issue detection with limited blast radius
- Clear rollback points

**Cons:**
- Slower time-to-value
- Multiple validation gates needed
- Complexity if data is interdependent

**ESWD Context:** Staged batches (Batch 1-2 small tables → Batch 3-6 large tables) naturally fits canary approach.

### 2.3 **Rolling Deployment** (Not suitable for data platforms)

Typically for microservices; not applicable to monolithic Delta lake.

### 2.4 **Big Bang** (The ESWD Approach)

Deploy everything in a single window. Requires flawless preparation.

**Pros:**
- Fastest deployment
- No intermediate states
- Simplest for single-schema deployments

**Cons:**
- High risk if any component fails
- Large blast radius
- No staged rollback

**Mitigated by:**
- Extensive pre-flight testing in Staging
- Detailed runsheet with clear checkpoints
- Fast rollback procedures
- Experienced team on standby

---

## Part 3: Deployment Phase Breakdown

### 3.1 Pre-Deployment Phase (T-1 week to T-1 day)

**Purpose:** Eliminate surprises and confirm readiness.

| Activity | Responsibility | Timing | Why It Matters |
|----------|-----------------|--------|-----------------|
| Code review & merge to main | Dev team | T-3 days | Avoids last-minute merge conflicts |
| Staging deployment & full test cycle | QA + Dev | T-2 days | Catches most issues early |
| Load test on expected Prod data volume | Data engineer | T-2 days | Reveals performance bottlenecks |
| CAB review & approval | CAB | T-1 day | Legal/governance gate |
| Runbook walkthrough with team | Deployment lead | T-1 day | Align on steps, timing, escalation |
| Backup & archive previous version | Release engineer | T-4 hours | Enables instant rollback |
| Smoke test of CI/CD pipeline (dry-run) | DevOps | T-2 hours | Confirm deployment automation works |
| Environment pre-checks (disk space, cluster status) | Ops | T-1 hour | Avoid environmental surprises |

### 3.2 Deployment Phase (Execution)

**W-1: Fivetran Ingestion** (Wed 5:00pm – Thu 7:00am)
- Create connection + ingest Batch 1-2 (small, fast)
- Continue Batch 3-6 overnight (large tables, auto)
- Monitor dashboard; document any failures

**W-2: Validation** (Thu 7:00am – 9:00am)
- Confirm all syncs completed
- Count reconciliation: ServiceNow vs. DARE landing
- **Go/no-go checkpoint** — stop if data validation fails

**W-3: CI/CD Deployment** (Thu 9:15am – 12:30pm)
- Deploy code (notebooks, Python, SQL, YAML)
- Execute deployment steps sequentially (schemas → watermarks → tables → views)
- Monitor each Databricks job for errors

**W-4: Validation (Post-deployment)** (Thu 12:30pm – 2:15pm)
- First-run checks (imports, tables exist, audit logs correct)
- Power BI sense-check
- **Go/no-go review** — declare success or escalate to rollback

**W-5: Contingency & Closure** (Fri 9:00am – 5:00pm)
- If needed, execute rollback
- Final validation
- CR closure and stakeholder notification

### 3.3 Post-Deployment Phase (T+1 to T+30 days)

| Activity | Who | Frequency | Goal |
|----------|-----|-----------|------|
| Monitor job run duration & error rates | Ali | Daily for 1 week | Catch performance regressions |
| Check watermark table for staleness | Ali | Daily | Ensure refresh logic working |
| Review audit logs for anomalies | Jurgen | Daily | Spot data quality issues |
| Power BI report KPI validation | Tom/Roshney | Daily | Confirm user-facing data correct |
| Collect user feedback | Roshney | Ongoing | Identify missing business logic |
| Lessons learned meeting | Ali + team | T+7 days | Document what went well/poorly |

---

## Part 4: Risk Mitigation Strategies

### 4.1 Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Fivetran connection fails (ServiceNow ACL issue) | Medium | High | Fujitsu pre-confirms ACL; fallback PAT token ready |
| Record count mismatch (data lost in ingestion) | Low | High | Count reconciliation gate Thu 8am; escalate to Fujitsu |
| Databricks cluster out of memory (large table load) | Low | Medium | Test large tables in Staging; increase cluster size if needed |
| CI/CD pipeline deployment times out | Low | High | Run pipeline dry-run Wed evening; monitor execution Thu |
| Power BI fails to connect to curated layer | Low | Medium | Test Power BI connection in Staging; Unity Catalog ACL pre-verified |
| Rollback incomplete (data partially restored) | Very Low | Critical | Archive previous version code + Delta table versions before deployment |
| Team unavailable during contingency (Fri) | Low | Medium | Named backups (secondary Jurgen, secondary QA) identified up-front |

### 4.2 Blast Radius Containment

**Scope Isolation:**
- New tables isolated in separate schemas (`eswd_ohs_assessment`, `erbi_global`)
- Existing DARE tables (ERBI staging) NOT modified
- Existing jobs (ERBI landing→staging) remain unchanged
- Power BI reports for other projects unaffected

**If deployment fails:**
- Drop new schemas; no harm to existing catalog
- Roll back Fivetran ingestion (delete landing tables)
- Re-enable previous version's jobs and views
- DARE users see no impact (still have old views if needed)

### 4.3 Data Loss Prevention

**Before modifying any table in rollback:**
1. Confirm the previous version code is deployed (tested)
2. Identify the correct Delta table version to restore to (if needed)
3. Get explicit approval from Ali + CAB rep before executing restore
4. Document the before/after state of every table

**Key principle:** Never manually drop or restore tables without approval — always use automation (rollback scripts).

---

## Part 5: Communication & Documentation

### 5.1 Stakeholder Communication Plan

| Stakeholder | Message Frequency | Preferred Channel | Message Content |
|-------------|-------------------|-------------------|-----------------|
| CAB / Management | Before + after | Email | Approval status, go/no-go decisions, closure |
| Deployment Team (Ali, Jurgen, Chiran) | Real-time | Slack channel | Step completion, blockers, status updates |
| Business Users (Tom, Roshney) | At key gates | Teams meeting | Go/no-go checkpoints, readiness, results |
| Fujitsu | Before + during | Email + call | ACL confirmation, Fivetran dashboard link, escalation |
| DARE User Community | T+1 hour post-go-live | All-hands email | Data now available, Power BI connection steps |

### 5.2 Runsheet as a Living Document

**During Deployment:**
- Print or share the runsheet in a shared spreadsheet (Teams, Slack)
- Fill in "Status" column in real-time (✓ Pass / ✗ Fail / ⏳ In Progress)
- Update "Completed By / Notes" with timestamp and owner
- If a step fails, immediately escalate (note the error, who to call)

**After Deployment:**
- Sign off with Deployment Lead, CAB rep, Fujitsan rep (Reference & Sign-Off sheet)
- Archive the filled-in runsheet in the CR record
- Include in lessons learned discussion

### 5.3 Escalation Matrix

| Issue | First Contact | If Unresolved (10 min) | If Still Unresolved (30 min) |
|-------|-----------------|------------------------|--------------------------------|
| Fivetran sync stalled | Ali | Fujitsan representative | Defer to Fri contingency |
| Databricks job error (code) | Jurgen | Ali (deployment lead) | Decide: continue or rollback |
| Data count mismatch | Chiran + Jurgen | Fujitsan (if source-side) | Escalate to CAB; may require re-ingestion |
| Power BI can't connect | Roshney | Ali (Unity Catalog ACL) | Defer BI validation to Fri |

---

## Part 6: Rollback Procedures

### 6.1 Rollback Decision Tree

```
Deploy Step Failed?
├─ YES: Error is in code (notebook, SQL, YAML)
│       └─ Action: Redeploy previous approved version (10 min)
│           └─ Continue with First-Run Checks
├─ YES: Error is in data (Fivetran didn't load, count mismatch)
│       └─ Action: Investigate + re-trigger Fivetran
│           └─ If unresolvable: Defer to Friday contingency window
└─ NO: Proceed to First-Run Checks

First-Run Checks Failed?
├─ YES: Table doesn't exist / data is null
│       └─ Action: Review deploy.sql logs; rerun missing steps
├─ YES: Row counts are wrong (duplicate rows, data loss)
│       └─ Action: Drop curated tables; rerun job_stg_cur_eswd_ohs_assessment
├─ YES: Performance unacceptable (Power BI timeout)
│       └─ Action: Increase cluster size; rerun first-run checks
└─ NO: Proceed to Go/No-Go Review
```

### 6.2 Full Rollback Procedure (if all else fails)

**Scope:** Complete revert to pre-go-live state (Thu 9:00am code + no ESWD data).

| Step | Action | Owner | Time | Proof |
|------|--------|-------|------|-------|
| 1 | Stop all Databricks jobs | Ali | 5 min | Job UI shows "Terminated" |
| 2 | Redeploy previous code (git tag) | CI/CD | 10 min | Pipeline run #X succeeded |
| 3 | Drop new schemas (eswd_*, erbi_*) | Ali | 5 min | SQL: `DROP SCHEMA ... CASCADE` |
| 4 | Recreate old reporting views | Ali | 5 min | Old views appear in catalog |
| 5 | Pause Fivetran ingestion | Ali | 2 min | Fivetran UI shows "Paused" |
| 6 | Delete landing tables (optional, if cleanup needed) | Ali | 5 min | Landing schema is empty |
| 7 | Rerun First-Run Checks against rolled-back code | Jurgen | 15 min | Old tables/views accessible |
| 8 | Notify stakeholders | Ali | 2 min | CR updated; Slack message sent |

**Total Time:** ~45 minutes from "rollback decision" to "previous version operational"

**Post-Rollback:**
- Do NOT re-attempt deployment until root cause is understood
- Conduct blameless post-mortem (Fri afternoon)
- Update runbook with learnings
- Obtain new CAB approval before re-scheduling

---

## Part 7: Lessons Learned & Continuous Improvement

### 7.1 Post-Deployment Review (T+7 days)

**Attendees:** Ali, Jurgen, Chiran, Tom, Roshney, CAB rep

**Agenda:**

1. **What went well?**
   - Smooth Fivetran ingestion? Fast validation? Clear communication?
   - Document as reusable practices

2. **What didn't go well?**
   - Delays, surprises, escalations?
   - Distinguish between process issues (fixable) and external factors (not)

3. **Data Quality Check**
   - Any ETL bugs discovered post-go-live?
   - Any business logic misalignments?
   - Were row counts/key uniqueness as expected?

4. **Performance Review**
   - Job run times consistent with Staging?
   - Cluster sizing adequate?
   - Power BI report refresh times acceptable?

5. **Action Items**
   - Owner + deadline for each improvement
   - Update runbook template for next deployment

### 7.2 Metrics to Capture

| Metric | Target | Actual | Impact |
|--------|--------|--------|--------|
| Deployment window duration | ≤ 48 hours | ? | Budget time |
| Number of go/no-go checkpoints passed on first attempt | 3/3 | ? | Process maturity |
| Number of escalations | ≤ 2 | ? | Readiness quality |
| Time to resolution (if rollback needed) | ≤ 1 hour | ? | Recovery speed |
| Post-deployment data anomalies | 0 | ? | Data quality |
| User satisfaction (survey) | ≥ 4/5 | ? | Business value |

---

## Part 8: Deployment Templates & Checklists

### 8.1 RECOMMENDED: Change Request (CR) Template

```
=== CHANGE REQUEST TEMPLATE ===

Title:           [Jira Ticket]-[Environment] – [Brief Description]
                 Example: DSP1-250-PROD – Deploy ESWD Phase 1 Curated Layer

Ticket #:        CHG5030730
Environment:     Production (DARE)
Change Type:     Scheduled Release (Planned)
Requested by:    Ali (Deployment Lead)
CAB Assigned:    [CAB Rep Name]

=== JUSTIFICATION ===
Business Reason: Enable ESWD Phase 1 health/safety/wellbeing reporting to school principals
Impact:          No impact to existing DARE users (new schemas, new data)
Scope:           14 ServiceNow EduSafe tables → landing → staging → curated
Budget:          Included in FY2026 DARE roadmap

=== RISK ASSESSMENT ===
Risk Level:      Low (staging-only until go-live; no existing DARE changes)
Blast Radius:    Contained to new eswd_* and erbi_* schemas
Mitigation:      Pre-tested in Staging; rollback procedure documented
Insurance:       Previous version code + Delta snapshots archived

=== DEPLOYMENT WINDOW ===
Requested Date:  Wed 26/08/2026 5:00pm AEST
End Window:      Fri 28/08/2026 5:00pm AEST
Maintenance:     2-day window; no concurrent maintenance
User Impact:     None during ingestion (Wed–Thu); new data available Fri

=== DEPLOYMENT ARTIFACTS ===
Runbook:         RFC_ESWD_Deployment_Runbook_-Prod.docx
Runsheet:        ESWD_Deployment_Runsheet_PROD.xlsx
Data Load Plan:  Implementation_plan_ESWD_DARE_go-live.xlsx
Code Branch:     feature/eswd-phase1-prod (merged to main, tested)
Rollback Plan:   Documented in Runsheet "Rollback Plan" tab

=== SIGN-OFF ===
Deployment Lead:   Ali ________________________  Date: ______
CAB Representative: ________________________     Date: ______
ServiceNow/Fujitsan: ________________________     Date: ______
```

### 8.2 RECOMMENDED: Runsheet Structure (5 Tabs Minimum)

Tab 1: **Pre-Flight Checklist**
- 10–15 critical items to verify before 5:00pm start
- Go/no-go decision box at bottom
- Completion date/owner column

Tab 2: **Deployment Runsheet** (Main)
- Columns: Time Start | Time End | Activity | Owner | Status | Notes/Evidence
- Section headers: Wed Ingestion | Thu Validation | Thu Deployment | Thu Validation | Fri Contingency
- ✓ / ✗ / ⏳ status indicators for real-time tracking

Tab 3: **First-Run Checks**
- 10–12 post-deployment validation items
- Reference to specific schemas, tables, audit logs
- Pass/Fail checklist

Tab 4: **Rollback Plan**
- 7–9 numbered steps
- Conditions for each step (if code failed, if data anomaly, etc.)
- Estimated time to completion

Tab 5: **Reference & Sign-Off**
- Key documentation links (CR, Jira, runbook file)
- Participant roster + contact info
- Signature blocks for deployment lead, CAB, Fujitsan

### 8.3 RECOMMENDED: Runbook Structure (Reference Document)

**Should be separate from runsheet. Use for "HOW" details; runsheet is "WHEN/WHO".**

Sections:
1. **Purpose** — What's being deployed and why
2. **What's Being Deployed** — Component list with descriptions
3. **Before Deployment** — Pre-flight manual steps (ACL setup, connection creation, etc.)
4. **Deployment Steps** — Sequential numbered steps with exact commands/notebooks/parameters
5. **First-Run Checks** — Post-deployment validation checklist
6. **Rollback Steps** — If deployment fails, step-by-step undo procedure
7. **Appendix** — Data dictionary, schema definitions, troubleshooting guide

**Format:** Word (.docx) or Confluence wiki (easier for updates)

---

## Part 9: Quick Reference Checklist

### Pre-Deployment (T-1 week)

- [ ] Code merged to main branch; automated tests passing
- [ ] Staging deployment successful; full test cycle complete
- [ ] CAB review scheduled; risk matrix reviewed
- [ ] Runbook and runsheet created and reviewed with team
- [ ] Rollback procedure tested (at least dry-run)
- [ ] Data load estimates captured (table counts, sync times)
- [ ] Stakeholder roster finalized; contact info confirmed
- [ ] Monitoring and alerting configured

### Pre-Deployment (T-1 day)

- [ ] CAB approval obtained and documented
- [ ] Previous version code archived in artifact repository
- [ ] Environment pre-checks run (disk space, cluster status, backups)
- [ ] CI/CD pipeline dry-run successful
- [ ] Runsheet printed/shared with team
- [ ] Team briefing completed; roles/escalations clear
- [ ] Fujitsan availability confirmed

### Deployment Execution (Wed–Fri)

- [ ] Pre-Flight Checklist completed; go/no-go decision made
- [ ] Runsheet updated in real-time as each step completes
- [ ] All go/no-go checkpoints (3 gates) passed on first attempt
- [ ] First-Run Checks 100% passed
- [ ] User acceptance test passed (Power BI sense-check)
- [ ] Deployment closure documented
- [ ] All participants sign-off obtained

### Post-Deployment (T+1 week)

- [ ] Monitoring confirmed (no errors, performance nominal)
- [ ] Lessons learned meeting conducted
- [ ] Runbook updated with new findings
- [ ] Template improvements documented for next deployment

---

## Part 10: Industry Best Practice Resources

### Standards & Frameworks

| Framework | Source | Relevance |
|-----------|--------|-----------|
| ITIL Change Management | AXELOS (gov.uk) | Overall change governance |
| DORA DevOps Metrics | Google Cloud | Deployment frequency, lead time, MTTR |
| CALMS Framework | Atlassian | Culture, Automation, Lean, Measurement, Sharing |
| CAP Theorem (for data systems) | Lynch & Brewer | Data consistency, availability, partition tolerance |

### Cloud Provider Best Practices

- **Databricks Best Practices:** https://docs.databricks.com/en/best-practices/ (Production readiness, cluster sizing, job monitoring)
- **Azure Data Lake (ADLS) Best Practices:** https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction (Access control, disaster recovery)
- **AWS Glue Deployment:** https://docs.aws.amazon.com/glue/latest/dg/best-practices.html

### Books & Guidance

- *The Phoenix Project* (Gene Kim et al.) — Change management & ITIL concepts
- *Continuous Delivery* (David Farley, Jez Humble) — Deployment pipelines, testing strategies
- *Accelerate* (Forsgren, Humble, Kim) — DORA metrics, high-performing teams

---

## Part 11: Conclusion

**For the ESWD Deployment:**

The runsheet you now have (`ESWD_Deployment_Runsheet_PROD.xlsx`) captures the **essence of industry best practices** in a single, executable document:

1. **Pre-Flight Checklist** = *Gate 1 (Build Gate)* — All preparation complete
2. **Deployment Runsheet** = *Gate 2 & 3 (Release Gate + Validation Gate)* — Execution + validation
3. **First-Run Checks** = *Post-deployment validation* — Confirm success
4. **Rollback Plan** = *Risk mitigation* — Fast undo if needed
5. **Reference & Sign-Off** = *Accountability* — Who approved, who executed

**Key Principles Applied:**
- ✓ Staged ingestion (canary: Batch 1-2 → Batch 3-6)
- ✓ Multiple go/no-go gates (Wed, Thu x2)
- ✓ Clear escalation matrix (Fujitsan for ServiceNow issues, DevOps for pipeline)
- ✓ Documented rollback procedures
- ✓ Post-deployment validation checklist
- ✓ Stakeholder communication plan

**This is production-grade change management. Follow it, document outcomes, iterate.**

---

**Last Updated:** 26 August 2026  
**Document Owner:** Ali (DARE Deployment Lead)  
**Next Review:** Post-deployment lessons learned (28 August 2026)
