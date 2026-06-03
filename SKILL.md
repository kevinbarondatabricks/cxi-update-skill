---
name: cxi-update
description: >
  IMMEDIATELY activate when user mentions CXI updates, summaries, or newsletters for any audience.
  This skill has THREE profiles - always determine which one before proceeding:
  (1) exec biweekly - triggered by "exec biweekly", "leadership update", or "exec summary"
  (2) weekly support / TSE update - the weekly Friday CXI Team Updates email - triggered by "weekly support", "support team update", "weekly summary", "TSE summary", "TSE update", or "CXI Team Updates"
  (3) exec biweekly v2 (EXPERIMENTAL swimlane / Q2 structure; additive sibling to exec biweekly, may be reverted) - triggered ONLY by "exec biweekly v2", "Q2 structure", or "swim lane structure". The plain "exec biweekly" triggers still route to profile (1).
  If invoked via /cxi-exec-summary-skill or ambiguously, ASK the user which profile before proceeding.
  Automatically aggregates context from Google Docs, JIRA, meetings, Gmail, and Slack.
triggers:
  - CXI exec biweekly
  - CXI leadership update
  - CXI exec summary
  - CXI exec biweekly v2
  - CXI Q2 structure
  - CXI swim lane structure
  - CXI weekly support
  - CXI support team update
  - CXI weekly summary
  - CXI TSE summary
  - CXI TSE update
  - CXI Team Updates
  - CXI update
version: 1.4.0
author: Kevin Baron-Quijano <kevin.baron@databricks.com>
---

# CXI Update Builder

## What This Skill Does
Generates audience-appropriate updates by aggregating from the same core sources and repackaging for different audiences. Same data, different lens.

**Sources:**
- Team Notes (from Google Suite via MCP)
- JIRA tickets (via MCP JIRA connection)
- Meeting notes (from Google Calendar via MCP)
- Gemini meeting summaries (from Gmail via MCP)
- Slack discussions (via MCP Slack connection)

**Two profiles (see `config.yaml` for full configuration):**

| | Exec Biweekly | Weekly Support / TSE Update |
|---|---|---|
| **Audience** | VP Support, COO, SVP Eng | All Support Engineering (TSEs, Tech Leads, Shift Leads, TSE Managers, Directors; plus IM/EM and Field Eng) |
| **Cadence** | Biweekly | Weekly (Fridays) |
| **Lens** | Outcomes, trends, asks | What's new, how to try it, where to ask |
| **Medium** | Google Doc | Email (Gmail draft), subject "CXI Team Updates" + companion Slack tease |
| **Template** | `biweekly-template.md` | `tse-update-email-template.md` |
| **Trigger** | "build CXI exec biweekly" | "build CXI weekly support" |
| **Alt triggers** | "leadership update", "exec summary" | "support team update", "weekly summary", "TSE summary", "TSE update", "CXI Team Updates" |

**Output:** Formatted Google Doc ready for review.

> **Third profile (EXPERIMENTAL):** `exec_biweekly_v2` is an additive pilot of a different exec structure (swimlane view + Q2 KPI movement + senior-leader asks). It reuses the exec biweekly audience, medium (Google Doc), and extraction phases; only the output structure differs. It does NOT replace `exec_biweekly` - both coexist, and v2 may be reverted. Triggered only by "exec biweekly v2" / "Q2 structure" / "swim lane structure". See the v2 branches in Phase 2 and Phase 3.

## Profile Selection (MANDATORY - always resolve before Phase 0)

**NEVER default to a profile. Always resolve explicitly.**

| User says | Profile |
|---|---|
| "exec biweekly", "leadership update", "exec summary", "biweekly" | `exec_biweekly` |
| "exec biweekly v2", "Q2 structure", "swim lane structure" | `exec_biweekly_v2` (EXPERIMENTAL) |
| "weekly support", "support team update", "weekly summary", "TSE summary", "TSE update", "CXI Team Updates" | `weekly_support` |
| `/cxi-exec-summary-skill` (bare invocation) | **ASK** |
| "CXI update" (ambiguous) | **ASK** |
| "build both" | Run both profiles sequentially |

When asking, use:
> Which CXI update do you want to build?
> 1. **Exec Biweekly** - for leadership (Sam, Hatim, Vinod)
> 2. **Weekly Support** - for Directors, TSE Managers, Shift Leads, Tech Leads, TSEs

> **CRITICAL: No hallucination policy.** If information for any section cannot be verified from the sources searched (JIRA, Slack, Google Docs, Calendar), do NOT fabricate or infer content. Instead, include the section header with: `CONFIRM MANUALLY - data not found in sources.` Always err on the side of leaving a section for manual review rather than generating unverified content. This applies to every section, including Roadmap status, Impact Signal metrics, What's Next items, and Ideas around Innovation from Support.

## Output Formatting Rules

These rules apply to ALL generated content in the Google Doc:

1. **Headers:** All section headers must be Heading 2, Arial, 16px, Bold
2. **No dividers:** Do not insert horizontal rules (`---`) or visual separators between sections
3. **No emojis:** Do not use emojis anywhere in the output - not in headers, not in TL;DR status, not in tables. Use bold plain text instead (e.g., **On Track** not a green circle emoji)
4. **Bold key points:** Emphasize deliverable names, metric values, status labels, and other key points in **bold**
5. **No em dashes:** Never use em dashes. Use regular dashes (-) or semicolons (;) instead
6. **TL;DR status format:** **On Track** / **Needs Attention** / **Off Track** (plain text, bold, no emojis)
7. **Roadmap status format:** **On Track** / **At Risk** / **Blocked** / **Deferred** (plain text, bold, no emojis)
8. **Paragraph spacing:** Add blank lines between paragraphs in What Shipped and Highlights sections for visual breathing room

## When to Use
- User requests CXI exec biweekly (for leadership)
- User requests CXI weekly support (for broader Support org)
- Time for recurring update (check `config.yaml` profiles for schedules)
- Ad-hoc summary needed for either audience

## Workflow

### Phase 0: Quarterly Pre-Read
**Goal:** Establish strategic context before gathering updates

0. **Quarterly Roadmap**
   - Use: `mcp__google__google_read_api_call` with endpoint `docs/documents/1llPhMHPMy0OVqK7E-qZjEEXNwKDo5Ipf52kJqwq9q7U`
   - Read the Roadmap section of the CXI Quarterly Pre-Read
   - Extract: Current quarter goals, key milestones, strategic priorities, and OKRs
   - Use this as the lens for interpreting and prioritizing all subsequent data (JIRA, Slack, meetings)
   - Frame exec summary highlights in terms of roadmap progress
   - Map each Roadmap Objective to its JIRA Epic (product area) using `roadmap_to_epic_mapping` in config.yaml
   - Group JIRA tickets by Epic, then report progress under the corresponding Roadmap Objective

### Phase 0.5: Read Planned Inclusions
**Goal:** Pick up manually-staged content items that should be folded into this run alongside the live data sources.

1. Read `planned-inclusions.md` at the skill root.
2. Under the `## Active` section, find every item whose `Profile:` matches the current run's profile AND whose `Target run:` date falls within the current run window (the upcoming run's target date, plus or minus 3 days).
3. Treat each matching item's `Content:` bullets as additional Phase 1 input that MUST be reflected in the output draft. Place each bullet in the section(s) listed under `Suggested sections:`; if the suggested section is missing from the template, fall back to the closest semantically equivalent section.
4. Linked JIRA keys called out in a planned inclusion are guaranteed-relevant - include them in any JIRA-derived sections (Highlights, Roadmap status) even if they would not have otherwise surfaced.
5. After the run is published successfully, move each consumed item from `## Active` to `## Consumed`, adding a `Consumed:` line with the run date and the published doc link. Do NOT auto-delete consumed items; the audit trail matters.
6. Items under `## Watch` are NOT in scope for the current run. Leave them alone unless their promotion criteria are clearly met, in which case surface them to Kevin for explicit promotion before including.

**Note:** the weekly Friday TSE update and the Non-Senior Leader Summary are the same artifact, served by the single `weekly_support` profile (the CXI Team Updates email). Planned-inclusion items targeting the TSE update use `Profile: weekly_support` and are consumed by this profile on its Friday run.

### Phase 1: Context Aggregation
**Goal:** Gather raw updates from all sources

> **weekly support / TSE update - PRIMARY SOURCE (most important).** This profile runs on **Day 10, after the Sprint Review**. Its single most important source is the Gemini Notes **TRANSCRIPTS** (the Transcript section of the doc, NOT the Summary/Notes/Details section, and NOT the Gmail "notes of CXI" summary emails) of the sprint's ceremonies:
> - **CXI Sprint Review** (Day 10, today)
> - the **4 CXI Standups** of the sprint (Day 3 Wed, Day 5 Fri, Day 7 Tue, Day 9 Thu)
> - **CXI Sprint Planning** (Day 1, the Monday prior)
>
> For each: find the calendar event (`calendar_search` / `calendar_event_list`), open its attached "Notes by Gemini" doc, and read the **Transcript** via `docs_document_export_as_markdown`. These transcripts are the narrative; the active-sprint JIRA **Done** list (JIRA step below) is the spine - the transcripts explain the story around each Done issue. Delegate transcript reading to sub-agents (one per ceremony, in parallel) so the raw transcripts do not bloat context; each sub-agent returns only TSE-relevant synthesis (what shipped and the "so what", recognition, feedback being prioritized, what's next, behavior changes like async usage). Do NOT shortcut this with the Gemini summary; the summary is lossy and has missed shipped items before.

1. **Meeting Notes (Calendar)**
   - Use: `mcp__google__calendar_event_list` tool
   - Query: Events from last 14 days matching configured keywords
   - Extract: Action items, decisions, key discussions

2. **Meeting Notes (Gemini Email Summaries)**
   - Use: `mcp__google__google_read_api_call` with endpoint `gmail/messages`
   - Query: `in:anywhere from:me subject:(notes of CXI) newer_than:14d`
   - The `in:anywhere` clause ensures Trash and Spam are included in the search
   - These are Gemini-generated meeting summaries emailed after CXI calls
   - Extract: Key discussion points, action items, decisions captured by Gemini
   - Cross-reference with Calendar events to fill gaps in meeting notes

3. **JIRA Tickets**
   - Use: `mcp__jira__jira_read_api_call` with endpoint `issues.search`
   - Query: `project = PLAT AND component = 'Support Platform' AND (status IN ("To Do","In Progress","In Review") OR updated >= -14d)`
   - Board: [Support Platform (board 6475)](https://databricks.atlassian.net/jira/software/c/projects/PLAT/boards/6475)
   - Prioritize: High-priority items, blockers
   - **weekly support / TSE update - systematic Done harvest (REQUIRED).** Harvest EVERYTHING marked Done in the active sprint: `project = PLAT AND component = 'Support Platform' AND status = Done AND sprint IN openSprints() ORDER BY updated DESC`. **Page through ALL results** (follow `nextPageToken`; do not stop at page 1). This complete Done list is the spine of "What We Shipped" - every shipped, practitioner-relevant item must be considered. Map each Done issue to its workstream; drop pure-internal items (auth hardening, refactors, logging) from the TSE-facing cut. The ceremony transcripts (primary source above) supply the narrative and "so what" for each Done issue.

3. **Slack Updates**
   - Use: `mcp__slack__slack_read_api_call` with endpoint `search.messages`
   - Strategy: Keyword-based search ("CXI", "support automation", "Isaac") across all channels, supplemented by `conversations.history` on configured channels
   - Channels: `#support-automation`, `#support-agent-internal`, `#eng-support-automation`, `#allhands-support`
   - Announcement pull: Also include any messages Kevin (kevin.baron@databricks.com) sent in `#eng-support-automation` that begin with `:announcement:`. These are guaranteed-relevant and must be reflected in the output draft. Search with `search.messages` using `from:@kevin.baron in:#eng-support-automation` and keep only messages whose text starts with the literal `:announcement:` token
   - Filter: Prioritize message content and relevance over reactions. Emoji reactions are a supplementary signal only; do not use them as a primary filter for inclusion

**Output:** Aggregated data structured by source

### Phase 2: Content Structuring
**Goal:** Transform raw data into narrative format appropriate for the selected profile

**Select template and style based on profile:**

#### If profile = exec biweekly:
1. **Apply Template:** `assets/templates/biweekly-template.md`
2. **Sections:** TL;DR, What Shipped, What this unlocks, Impact Signal, Risks / Gaps, Asks, Highlights, What's Next, Roadmap
3. **Style:** See `references/domain-context.md` - "Executive Summary Writing Style Guidance"
4. **Lens:** Outcomes, trends, strategic progress, cross-functional asks
5. **Altitude - NO JIRA in exec updates (HARD RULE):** Never include JIRA issue keys (e.g. PLAT-XXXXX), issue counts, or ticket-level granularity anywhere in the output. That is too granular for this audience (VP / SVP / COO). Use JIRA only as an internal source while gathering; in the output, speak in outcomes and impact and reference work by capability or workstream, not by issue. This applies to both exec profiles (exec_biweekly and exec_biweekly_v2).

#### If profile = exec biweekly v2 (EXPERIMENTAL - swimlane / Q2 structure):
> Additive sibling to exec biweekly; reuses Phases 0, 0.5, 1 verbatim. Only this structure differs. Do NOT alter the exec biweekly branch.
1. **Apply Template:** `assets/templates/biweekly-v2-template.md`
2. **Sections (exactly three):**
   - **What We Landed (by Swimlane)** - group the active-sprint Done harvest (Phase 1 JIRA) by the five swimlanes in `config.yaml` `profiles.exec_biweekly_v2.swimlanes`, using `swimlane_to_epic_mapping` (under `data_sources.jira`) to route each Epic's Done items to its lane. Render lanes in that order. A workstream that spans lanes (e.g. Support Tooling Service) is attributed to the lane its specific deliverable serves. Use the ceremony transcripts for narrative / "so what". **Report at outcome altitude with NO JIRA issue keys (too granular for leadership - see the HARD RULE in the exec biweekly branch above).** **Fold the forward-look into each lane:** end each lane's narrative with a brief "Looking ahead: ..." sentence covering what is next for that lane. The forward view lives inside the lanes; there is NO separate "What's Next" section. Each lane then ends with a **Blocked** line (where the lane is stuck, or "No blockers.").
   - **Q2 KPI Metrics That Moved** - the four Q2 P0 KPIs (rows are pre-filled in the template from the pre-read; KPI names, baselines, and end-of-Q2 targets are static). Fill This Period + Movement ONLY from the Data-Science-confirmed source per KPI. **Until DS confirms the tracking source (see `kpi_source_status` in config; DM to Sayan 2026-06-03), leave This Period + Movement as `CONFIRM MANUALLY - source pending DS`.** Do NOT fabricate KPI movement.
   - **Where We Need Help from Senior Leadership** - filter asks to what only **Hatim, Vinod, or Sam** can action. This is NOT a general blocker list - lane-level blockers already live in Section 1. Each ask: which leader, the ask, the impact, a by-when. If none, "No asks this period."
3. **Style:** See `references/domain-context.md` - "Executive Summary Writing Style Guidance"
4. **Lens:** Outcomes by swimlane (with a forward-look folded into each lane), committed-KPI movement, senior-leader unblocks
5. **No-hallucination:** any swimlane with no verifiable landed work says so; the KPI cells stay `CONFIRM MANUALLY` until sourced.
6. **Altitude:** the NO-JIRA hard rule from the exec biweekly branch applies here too - no issue keys or ticket-level detail in the output.

#### If profile = weekly support / TSE update:
1. **Apply Template:** `assets/templates/tse-update-email-template.md`
2. **Medium:** Render the draft INLINE in the CLI first for iteration; only after Kevin approves, produce a formatted (HTML) Gmail draft (subject "CXI Team Updates"; To/CC from `config.yaml` `weekly_support.email`) plus a companion Slack tease in `#eng-support-automation`. See Phase 3.
3. **Sections (5/9 scaffolding around the 7-point spine):** Salutation; Opening + "Bookmark go/cxi."; TL;DR; User feedback we are prioritizing (Aha, Enablement: Champions/Workshops, Support Agent feedback); What we shipped + how to try it; Recognizing Power Users (Reviewers, Champions, Demo'ers; fold in hackathon recognition when relevant); Welcoming new users (IM/EM, Field Eng); Best Practices (go/cxi Page: links); Upcoming bootcamps; What's next; Getting help; closing
4. **Style:** See `references/domain-context.md` - "Weekly Support Update Writing Style Guidance"; plus the conventions in the `reference_tse_update_format` memory (subject line, "go/cxi, Page: <title>" link style)
5. **Lens:** What changed in workflows, how to use it, where to ask questions
6. **Data filtering:** From the same Phase 1 data, extract only items with a practitioner-facing impact - skip internal architecture changes, roadmap strategy, or leadership asks. Exclude Merlin and SupportBricks from "What's next"
7. **Sourcing:** Build "What we shipped" from the COMPLETE active-sprint Done list (Phase 1 JIRA harvest), using the ceremony **transcripts** as the narrative and "so what" for each item. Recognition, feedback being prioritized, and what's next come primarily from the transcripts. Do not rely on the Gemini summary.

**Shared best practices (both profiles):**
- Follow Output Formatting Rules (see above)
- No emojis anywhere
- No em dashes; use regular dashes and semicolons
- Bold key points throughout
- All headers: Heading 2, Arial, 16px, Bold
- No dividers between sections

**Format for Medium (by profile):**
- **exec biweekly -> Google Doc:** Use template-based approach
  1. Copy pre-formatted template: `mcp__google__drive_file_copy` (use `template_id` from the active profile in config.yaml)
  2. Replace content: `mcp__google__docs_document_batch_update` with `replaceAllText` requests
  3. IMPORTANT: Use plain text only - NO HTML tags (they render as literal text)
- **exec biweekly v2 (EXPERIMENTAL) -> Google Doc:** Same approach as exec biweekly, but copy the v2 `template_id` and replace the v2 token set (swimlane Landed/Blocked, KPI cells, senior asks). Original exec biweekly template untouched.
- **weekly support / TSE update -> inline first, then HTML email:** Render the draft inline in the CLI (markdown) for iteration. Only after Kevin approves, create a Gmail draft as **formatted HTML** (via the `raw` MIME parameter; NOT plain text) using subject, To, and CC from `config.yaml` `weekly_support.email`. Do NOT send. Then draft the companion Slack tease for `#eng-support-automation` (single `*` bold, no LLM footer).

**Output:** Structured draft with all sections populated

### Phase 3: Review and Delivery
**Goal:** Finalize and distribute

#### If profile = exec biweekly (Google Doc)
1. **Generate Draft Using Template**
   - Copy template: `mcp__google__drive_file_copy` with `template_id` from the active profile in config.yaml
   - Update title: Use `replaceAllText` to set date/week
   - Replace content sections: Use multiple `replaceAllText` requests (NO HTML tags!)
   - All formatting (headers, tables, etc.) is preserved from template

   **CRITICAL:** Never use HTML tags like `<b>`, `<table>`, etc. in `replaceAllText` - they render as literal text. Template already has all formatting; just replace text content.

2. **Tag Reviewers** - Primary: kevin.baron@databricks.com; Secondary: samira.emmerson@databricks.com (from `config.yaml`)
3. **Send Notification** - Post to Slack `chat.postMessage` to the profile's `slack_notification_channel`; Format: "{profile_name} draft ready for review: {LINK}"

#### If profile = exec biweekly v2 (EXPERIMENTAL - Google Doc)
> Same delivery mechanism as exec biweekly, with the v2 profile's own `template_id`. Does NOT touch the exec biweekly template or Doc.
1. **Generate Draft Using Template**
   - Copy template: `mcp__google__drive_file_copy` with `template_id` from `profiles.exec_biweekly_v2` in config.yaml (the v2 Doc, NOT the exec biweekly Doc)
   - Update title via `replaceAllText` using `title_format` ("CXI Q2 Executive Summary (Swimlane View) - {date}")
   - Replace content with `replaceAllText` (NO HTML tags). Token set is in `assets/templates/biweekly-v2-template.md`: the per-swimlane `{LANE_*_LANDED}` / `{LANE_*_BLOCKED}` tokens, the KPI `{KPI*_THIS_PERIOD}` / `{KPI*_MOVEMENT}` cells, and `{SENIOR_ASKS}`
   - **Leave the KPI This-Period / Movement cells as `CONFIRM MANUALLY - source pending DS` until Data Science confirms the tracking source** (see `kpi_source_status` in config). Do not fabricate KPI numbers.
2. **Tag Reviewers** - Primary: kevin.baron@databricks.com; Secondary: samira.emmerson@databricks.com
3. **Send Notification** - Post to the profile's `slack_notification_channel` (#support-automation); Format: "{profile_name} draft ready for review: {LINK}"

#### If profile = weekly support / TSE update
1. **Render the draft INLINE in the CLI first.** Output the full email body as markdown in the conversation, built from the ceremony transcripts + complete Done list. Do NOT create a Gmail draft yet. Iterate inline with Kevin until he says it is ready. Mark any section lacking a verified source as `CONFIRM MANUALLY`.
2. **Only after Kevin approves the content:** create the email as a Gmail draft with `mcp__google__gmail_draft_create`, subject ("CXI Team Updates"), To and CC from `config.yaml` `weekly_support.email`, rendered as **formatted HTML** via the `raw` MIME parameter (NOT plain text). DO NOT SEND - leave it as a draft for Kevin to send himself.
3. **Companion Slack tease** - draft for `#eng-support-automation` (single `*` bold, no LLM footer; exclude Merlin per channel norm). Post only after Kevin's explicit confirmation.
4. **Refresh the go/cxi Best Practices page (after the email is finalized).** Update the *Support Agent Best Practices* Confluence page (`page_id` 6209308108, space UN; see `config.yaml` `links.best_practices_page_id`) with any **durable, practitioner-facing best practices** surfaced in this update - new usage guidance, behavior changes, command/tool changes - mapped to its existing sections (**Overall**, **Writing Skills**, **Using Skills**). Draw from the same inputs as the email PLUS any Confluence pages created that week and Kevin's `#eng-support-automation` announcements. Rules:
   - Preserve all existing content; only add or revise.
   - Replace now-stale guidance and keep phrasing **version-resilient** (e.g., "use the latest Opus and how to find it," not a pinned model version).
   - Exclude time-bound announcements and in-flight / experimental links (e.g., Learn Mode onboarding) until they conclude.
   - Fetch raw storage (`get_confluence_page_content` with `is_full=true`, `write_to_file=true`), edit, and write back with `mcp__confluence__update_confluence_page` (increment `version_number`, `include_footer=false`).
   - **External write - propose the changes and get Kevin's explicit confirmation before applying.** If Confluence 403s on scopes, hand Kevin the storage-format section to paste.

**Output:** exec biweekly -> published Doc URL; weekly support / TSE update -> inline CLI draft for iteration, then (on approval) a formatted-HTML Gmail draft + companion tease text, then (on approval) the refreshed go/cxi Best Practices page

## Success Criteria

### Profile: exec biweekly

#### Example Input
```
User: "Build the CXI exec biweekly"
```

### Expected Output
1. Google Doc created with title "CXI Executive Summary - [date range]"
2. Contains:
   - TL;DR - **On Track** / **Needs Attention** / **Off Track** status + one sentence; derived from Roadmap, Risks / Gaps, and Asks state
   - What Shipped - what moved this sprint, tied to the quarterly goal; space between paragraphs
   - What this unlocks - impact framing (bullet list)
   - Ideas around Innovation from Support - Aha! ideas table (manual fill)
   - Impact Signal - metrics with baselines and targets
   - Risks / Gaps - dependencies and constraints
   - Asks - cross-functional requests with who, what, and when
   - Highlights - demos, deliverables, recordings/links; space between paragraphs
   - What's Next - concrete next-sprint items; if no data found, include the section header with a note to fill in manually before sending
   - Roadmap - table of roadmap objectives with Status and Progress updated from JIRA epic state via `roadmap_to_epic_mapping`
3. No emojis anywhere in the document
4. No em dashes; only regular dashes and semicolons
5. All headers: Heading 2, Arial, 16px, Bold
6. Key points bolded throughout
7. No horizontal dividers between sections
8. Reviewers tagged and notified
9. Link shared in Slack

### Sample Generated Content
```markdown
# CXI Executive Summary - March 19, 2026

## TL;DR
**On Track** - All roadmap items progressing; one ask pending from IT (Salesforce write access) but not blocking current sprint.

## What Shipped
Sprint 3 delivered **Support Agent MCP integration** and **Merlin auto-collection** for DBSQL workspace diagnostics.

The integration enables TSEs to run diagnostic workflows directly from the agent interface, closing the loop on the Q1 goal of making DBSQL support executable.

**What this unlocks:**
- TSEs can trigger diagnostic collection without manual steps
- Isaac-based investigation is now measurable end-to-end

### Ideas around Innovation from Support
| Idea | Submitted By | Outcome |
|------|-------------|---------|
| Auto-collect DBSQL query plans on case creation | @jane-doe (FL DBSQL) | Shipped in Merlin v2 |
| Custom diagnostic templates per product area | @john-smith (BL) | Not now - scope too broad for Q1; revisit in Q2 prioritization |

Spotlight: @jane-doe's query plan gap feedback shaped Merlin's collection logic.

## Impact Signal
- **TTM baseline:** 5.1 days; targeting 5% improvement by end of Q1
- **AI SAT eval pass rate:** 72% (up from 65% last sprint)

## Risks / Gaps
No material blockers. Key dependencies:
- IT / BSE for Salesforce write integration

## Asks
- **Ask (IT):** Prioritize Salesforce write access by April 1 for Phase 2 rollout

## Highlights
**March 16 demo with TSEs** - Support Agent + TSE-facing evaluation UI for NBA, case summarization, similar cases and AI-SAT.

**Merlin DBSQL Auto-Collection** - First version of DBSQL diagnostic collection is now live from minimal customer input.

## What's Next
- Isaac agent rollout to tier-2 DBSQL support
- Q1 metrics review with Sam

## Roadmap
| Roadmap Objective | Status | Progress |
|-------------------|--------|----------|
| Automatic Diagnostic Collection (DBSQL) | **On Track** | Merlin v2 shipped; auto-collection live |
| Governed Execution Plane | **At Risk** | MCP integration PR in review; blocked on auth |
| Isaac Investigation Surface | **On Track** | Support Agent measurable end-to-end |
| Support Tooling Backend Service | **Deferred** | Deprioritized for Q1; revisit Q2 |
```

### Profile: weekly support / TSE update

#### Example Input
```
User: "Build the CXI weekly support"  (also: "build the TSE update", "build CXI Team Updates")
```

#### Expected Output
1. Draft rendered INLINE in the CLI for iteration; a formatted-HTML Gmail draft (subject "CXI Team Updates", To/CC from `config.yaml` `weekly_support.email`) is created only AFTER Kevin approves the content; never auto-sent
2. Body contains, in order: Salutation; Opening + "Bookmark go/cxi."; TL;DR; User feedback we are prioritizing; What we shipped + how to try it; Recognizing Power Users; Welcoming new users; Best Practices; Upcoming bootcamps; What's next; Getting help; closing
3. Same formatting rules as exec biweekly (no emojis, no em dashes, bold key points), plus the "go/cxi, Page: <title>" link convention; Merlin and SupportBricks excluded from What's next
4. Companion Slack tease drafted for `#eng-support-automation` (not posted)
5. Nothing sent or posted without Kevin's explicit confirmation
6. After the email is finalized, the go/cxi **Support Agent Best Practices** page (6209308108) is refreshed with durable practitioner best practices from the update (version-resilient phrasing; in-flight/experimental items held); proposed first, applied only on Kevin's confirmation

#### Sample Generated Content
The live structure and per-section guidance are in `assets/templates/tse-update-email-template.md` (the email body) and the `reference_tse_update_format` memory (subject line, link conventions, recurring sections). Build the draft from those rather than a hardcoded sample.

## Configuration

### Required MCP Connections
Ensure these MCP servers are configured in your Claude Code settings:

```bash
# Google MCP (for Calendar, Docs, Gmail)
# See: https://github.com/anthropics/mcp-servers/google

# JIRA MCP
# See: Your organization's JIRA MCP documentation

# Slack MCP
# See: Your organization's Slack MCP documentation
```

### Skill Configuration
**See `config.yaml` for full configuration.** Key structure:

```yaml
# Two audience profiles sharing the same data sources
profiles:
  exec_biweekly:
    schedule: biweekly
    audience: "VP Support, COO, SVP Eng"
    template_id: "..."  # Google Doc template for exec biweekly
    markdown_template: "assets/templates/biweekly-template.md"
  weekly_support:
    schedule: weekly          # Fridays
    audience: "All Support Engineering (TSEs, Tech Leads, Shift Leads, TSE Managers, Directors; plus IM/EM and Field Eng)"
    output_format: email      # Gmail draft, subject "CXI Team Updates"; see email: block in config.yaml
    markdown_template: "assets/templates/tse-update-email-template.md"

# Shared data sources (used by both profiles)
data_sources:
  meetings: ...
  gmail: ...
  jira: ...
  slack: ...
```

## Troubleshooting

### Common Issues
- **"No meeting notes found"** - Check `calendar_id` in `config.yaml`
- **"JIRA query returned 0 tickets"** - Verify `project_key` matches your JIRA project
- **"Slack scraper failed"** - Ensure MCP Slack connection is authenticated

See `references/mcp-integration.md#troubleshooting` for detailed solutions.

## Extending This Skill

### Add New Data Source
1. Identify the MCP tool (e.g., `mcp__confluence__search_confluence_pages`)
2. Add configuration to `config.yaml`
3. Update Phase 1 in this file with the new tool call
4. Modify template to include new section

### Change Output Format
1. Duplicate template: `assets/templates/custom-template.md`
2. Update Phase 2 to reference new template
3. Update `config.yaml` to point to new template
4. Test with: "Build CXI exec summary using custom template"

## Quick Start Customization Checklist

Before using this skill, update:

- [x] YAML frontmatter: `name`, `description`, `triggers`, `author`
- [x] `config.yaml`: Profiles, data sources, team-specific values
- [x] `assets/templates/biweekly-template.md`: Exec biweekly template
- [x] `assets/templates/tse-update-email-template.md`: Weekly support / TSE update email template
- [x] `references/domain-context.md`: Style guidance for both profiles
- [x] weekly_support is an email profile (Gmail draft); no Google Doc template_id needed
- [ ] Test: Say "Build CXI exec biweekly" and verify exec biweekly profile triggers
- [ ] Test: Say "Build CXI weekly support" (or "build the TSE update") and verify weekly support profile triggers

## References
- Team context and style guide: `references/domain-context.md`
- MCP tool usage guide: `references/mcp-integration.md`
- More examples: `references/examples/`
