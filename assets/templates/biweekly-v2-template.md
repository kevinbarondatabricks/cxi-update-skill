# {TEAM_NAME} | Q2 Executive Summary (Swimlane View) | {DATE}

<!-- =====================================================================
  EXPERIMENTAL v2 STRUCTURE for the exec leadership update.
  Additive sibling to biweekly-template.md - does NOT replace it.
  Profile: exec_biweekly_v2 (triggers: "exec biweekly v2", "Q2 structure",
  "swim lane structure"). May be reverted; keep biweekly-template.md intact.

  Reuses the SAME extraction phases as exec_biweekly (Phase 0 roadmap read,
  Phase 0.5 planned inclusions, Phase 1 source aggregation). Only the OUTPUT
  structure below is different: three sections instead of the v1 set.

  PLACEHOLDERS TO REPLACE:
    {TEAM_NAME}   - "CXI"
    {DATE}        - Period end date (e.g., "June 12, 2026")
    {GO_LINK}     - "go/cxi"

    Section 1 (per swimlane; one Landed token + one Blocked token each):
    {LANE_AUTOMATION_LANDED} / {LANE_AUTOMATION_BLOCKED}
    {LANE_SUPPORTOPS_LANDED} / {LANE_SUPPORTOPS_BLOCKED}
    {LANE_CI_LANDED} / {LANE_CI_BLOCKED}
    {LANE_MERLIN_LANDED} / {LANE_MERLIN_BLOCKED}
    {LANE_SUPPORTBRICKS_LANDED} / {LANE_SUPPORTBRICKS_BLOCKED}

    Section 2 (Q2 KPI movement; KPI names + targets are static from the
    pre-read, the live cells are tokens):
    {KPI1_THIS_PERIOD} / {KPI1_MOVEMENT}
    {KPI2_THIS_PERIOD} / {KPI2_MOVEMENT}
    {KPI3_THIS_PERIOD} / {KPI3_MOVEMENT}
    {KPI4_THIS_PERIOD} / {KPI4_MOVEMENT}

    Section 3:
    {SENIOR_ASKS}

  FORMATTING RULES (for Google Doc output):
    - All section headers: Heading 2, Arial, 16px, Bold
    - Swimlane sub-headers: Heading 3
    - No horizontal rules or dividers between sections
    - No emojis anywhere; no em dashes (use - or ;)
    - Bold key points (deliverable names, metric values, status labels)
    - Blocker status labels: **On Track** / **At Risk** / **Blocked** (plain bold)
===================================================================== -->

**Self-Link:** {GO_LINK}

## What We Landed (by Swimlane)

<!-- Group what shipped this period by the five Q2 swimlanes (matrix column order:
     Automation, Support Ops, CI, Merlin, Support Bricks). Source = the active-sprint
     JIRA Done harvest (Phase 1) mapped to each lane's workstream Epics, with the
     ceremony transcripts supplying the narrative and "so what". A workstream that
     spans lanes (e.g. Support Tooling Service) reports under the lane the specific
     deliverable serves.
     ALTITUDE: outcome-level only - NO JIRA issue keys (e.g. PLAT-XXXXX) or ticket-level
     detail anywhere (too granular for VP/SVP/COO). Reference work by capability/workstream.
     FORWARD-LOOK: fold the forward view INTO each lane - end each lane's LANDED narrative
     with a brief "Looking ahead: ..." sentence. There is NO separate What's Next section.
     Then end each lane with a Blocked line (where the lane is stuck, or "No blockers.").
     If a lane had no landed work this period, say so. -->

### Automation
{LANE_AUTOMATION_LANDED}

**Blocked:** {LANE_AUTOMATION_BLOCKED}

### Support Ops
{LANE_SUPPORTOPS_LANDED}

**Blocked:** {LANE_SUPPORTOPS_BLOCKED}

### CI
{LANE_CI_LANDED}

**Blocked:** {LANE_CI_BLOCKED}

### Merlin
{LANE_MERLIN_LANDED}

**Blocked:** {LANE_MERLIN_BLOCKED}

### Support Bricks
{LANE_SUPPORTBRICKS_LANDED}

**Blocked:** {LANE_SUPPORTBRICKS_BLOCKED}

## Q2 KPI Metrics That Moved

<!-- The four Q2 P0 KPIs from the FY27 pre-read (Q2 tab, KPIs section). KPI names,
     baselines, and end-of-Q2 targets are static; fill This Period + Movement from
     the authoritative source per KPI once Data Science confirms it.
     STATUS: tracking source per KPI is PENDING Data Science (Sayan / Olarn / Devyn);
     until confirmed, leave This Period + Movement as "CONFIRM MANUALLY - source pending DS". -->

| KPI (Category) | Baseline (per pre-read) | End-of-Q2 Target | This Period | Movement |
|---|---|---|---|---|
| % of Cases solved by Support Agent (DELIVER) | 2% | 20% | {KPI1_THIS_PERIOD} | {KPI1_MOVEMENT} |
| % of Cases Investigated with Skill Invocations - Funnel Metric 2 (DELIVER) | DBSQL L14d 90% | 85% of all cases, by 100% of TSEs | {KPI2_THIS_PERIOD} | {KPI2_MOVEMENT} |
| Median TTM / automated diagnostic data collection (DEFLECT) | DBSQL 1.2 days | Compute + AI Platform 10% reduction | {KPI3_THIS_PERIOD} | {KPI3_MOVEMENT} |
| Active MTTM / Agentic Mitigation (DELIVER) | DBSQL 3.2 days | All cases 5% decrease | {KPI4_THIS_PERIOD} | {KPI4_MOVEMENT} |

<!-- Source of truth and per-KPI computation/refresh cadence: PENDING Data Science.
     Tracking ownership is a Samira ask (one central place, DS-owned); DM sent to
     Sayan 2026-06-03. Wire each row to its confirmed source when DS replies. -->

## Where We Need Help from Senior Leadership

{SENIOR_ASKS}

<!-- ONLY asks that Hatim, Vinod, or Sam can action - not a general blocker list
     (lane-level blockers already live in Section 1). Each ask: which leader, the
     ask, the impact if unaddressed, and a by-when. Format:
     - **Ask (Hatim):** <what> by <when> - <impact>.
     If there are no senior-leader asks this period, state "No asks this period." -->

## Validation Checklist

Before sharing, verify:
- [ ] All `{PLACEHOLDERS}` replaced (or explicitly marked `CONFIRM MANUALLY`)
- [ ] Section 1 covers all five swimlanes; each has a Landed block, a folded "Looking ahead" forward-look, and a Blocked line
- [ ] No JIRA issue keys or ticket-level detail anywhere (exec altitude)
- [ ] Section 2 KPI rows reflect real movement from the DS-confirmed source (not stubbed)
- [ ] Section 3 asks are senior-leader-actionable only (Hatim / Vinod / Sam), each with who/what/when
- [ ] No emojis; no em dashes; key points bolded
- [ ] All section headers Heading 2; swimlane sub-headers Heading 3
- [ ] No dividers between sections
