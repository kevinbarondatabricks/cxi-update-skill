# Planned Inclusions for Upcoming CXI Updates

Manually staged content items that should be folded into specific upcoming CXI Update runs in addition to the live Calendar / Gmail / JIRA / Slack data sources.

The skill reads this file as **Phase 0.5** of each run (see `SKILL.md`). When the target run date for an item falls within the current run window (typically the next 14 days), include the content in the named profile and the suggested sections. After the run successfully publishes, move the item from `## Active` to `## Consumed` with a `Consumed:` date so the staged list does not grow forever.

`Watch` items are conditional or future-dated; they stay here until ready to promote to `Active`.

## Active

### Mulesoft prod rollout milestone - Exec Summary Tuesday 2026-06-02

- **Profile:** `exec_biweekly`
- **Target run:** 2026-06-02
- **Added:** 2026-05-28 by Kevin Baron-Quijano
- **Source:** [Slack thread in #support-automation](https://databricks.slack.com/archives/C0AB4NQN4R5/p1779902956707399)
- **Linked JIRA:** PLAT-162101, PLAT-162396, PLAT-161903
- **Suggested sections:** What Shipped, What this unlocks, Impact Signal

**Content:**

- **Mulesoft prod milestone:** ACAB review expected complete Thursday 5/28; Mulesoft production rollout targeted Friday 5/29.
- **SDR-4150 closure:** Full SDR review of the support tooling work marked done by security champions; ready to roll out once Mulesoft + SECEXP land.
- **Downstream Support Agent / Sherpa relaunch path:** End-to-end re-test of the Support Agent / Sherpa integration immediately follows the Mulesoft prod rollout, leveraging Sherpa pre-warming on case creation.
- **AI SAT live computation as a manager-workflow win:** The Mulesoft event flow into Sherpa unblocks live AI SAT calculation for manager workflows, addressing a long-standing manager ask. (Confirming with Sayan Sanyal whether live AI SAT lights up automatically with the Mulesoft prod rollout or has additional dependencies; pending answer affects how concretely to frame this.)

### Q2 DRI announcements - Exec Summary Tuesday 2026-06-02

- **Profile:** `exec_biweekly`
- **Target run:** 2026-06-02
- **Added:** 2026-05-28 by Kevin Baron-Quijano
- **Source:** [Q2 Deliverables and DRIs meeting 2026-05-20 Gemini Notes](https://docs.google.com/document/d/1s2C1-7AB-De43ac8FMXL43nHJkrDNXeCAejLGkDM_yY/edit)
- **Suggested sections:** Q2 Plan, Ownership / DRIs

**Content:**

- Per the 2026-05-20 alignment with Samira, Manoj, Scott, and Max, name the DRI for each Q2 workstream in this exec summary. Q2 organizes work around DRIs who own P&L, visibility, and dependencies for their workstream - this is the right forum to make the assignments visible to Sr Leadership.
- **DRIs to specify** (cross-reference with the four P0-P2 tables Samira already ported into the CXI Internal Team Doc; pull DRI column verbatim):
  - Support Agent: Skill Catalog & Migration (PLAT-160150) - Manoj Koushik (with Hari Kunhumveettil)
  - Support Agent: Skill CI & Self-Validation (PLAT-160151) - Ajmal Yusuf
  - Support Agent: Telemetry & Adoption Measurement (PLAT-160152) - Jacob Plachta
  - Support Agent: UX & Command Surface (PLAT-160153) - Manoj Koushik
  - Support Agent: External Integrations (PLAT-160154) - Vikash Choudhary
  - Support Agent: Productionization & Compliance (PLAT-160155) - Jacob Plachta + Kevin Baron
  - Support Agent MCP (PLAT-156366) - Vikash Choudhary
  - Merlin (PLAT-119961) - Chetan Bhadricha
  - Merlin Devloop (PLAT-145096) - Scott Oseychik (DRI / PS); primary engineer Vidhi Khaitan
  - Merlin Diagnostics Auto-Collection (PLAT-149983) - Scott Oseychik (DRI / PS); primary engineer Vidhi Khaitan
  - Support Tooling Service (PLAT-152062) - Jacob Plachta
  - SupportBricks (PLAT-157199) - Max Ramsay
- **Framing note:** the previously-discussed pilot of surfacing DRIs at Max's Ops Center kickoff (2026-05-29) is NOT the right forum - that meeting's scope is the Support Ops Command Center kickoff, not CXI workstream ownership. Use the exec summary instead.
- **Cross-reference:** the related operating decisions (P0/P1/P2 framework, three KPIs, quality split at PR merge, DRIs-scope-own-work, escalation as a tool) are captured in the memory system under their respective files; this exec summary inclusion is the formal external-facing announcement of the DRI assignments.

### Mulesoft prod rollout - what changes for managers - Non-Senior Leader Summary Tuesday 2026-06-02

- **Profile:** `weekly_support`
- **Target run:** 2026-06-02
- **Added:** 2026-05-28 by Kevin Baron-Quijano
- **Source:** [Slack thread in #support-automation](https://databricks.slack.com/archives/C0AB4NQN4R5/p1779902956707399)
- **Linked JIRA:** PLAT-162101, PLAT-162396, PLAT-161903
- **Suggested sections:** What's New, Known Limitations, Questions / Feedback

**Content:**

- Mulesoft prod rollout (targeted **Friday 5/29**) enables real-time Salesforce case events to flow into Sherpa, which means:
  - Faster Support Agent responses on new cases, because Sherpa pre-warms case context the moment a case is created.
  - **AI SAT can now be computed live** on cases, directly answering a long-standing manager ask for manager-workflow visibility.
- **Known limitation today:** no immediate change to TSE workflows on day one; the Support Agent / Sherpa end-to-end relaunch follows the rollout.
- **Where to ask:** broader broadcast lands on `#allhands-support` and via the next Friday TSE Summary once the relaunch is verified.

## Watch

### Support Agent / Sherpa relaunch mention - Friday TSE Summary, post-verification

- **Profile:** `weekly_support` (the weekly Friday CXI Team Updates email)
- **Target run:** First Friday TSE Summary AFTER the Mulesoft prod rollout AND the Support Agent / Sherpa end-to-end re-test are both verified. Earliest plausible date: 2026-06-05. If verification slips, this item slips with it.
- **Added:** 2026-05-28 by Kevin Baron-Quijano
- **Source:** [Slack thread in #support-automation](https://databricks.slack.com/archives/C0AB4NQN4R5/p1779902956707399)
- **Linked JIRA:** PLAT-161903 (gating ticket - "Deploy SFDC Case Events to Prod, and Test MuleSoft Integration")
- **Promotion criterion:** Move from Watch to Active once PLAT-161903 hits Done AND the Support Agent / Sherpa integration has been re-tested end-to-end in prod. Lead with proof rather than promise.

**Content (draft, refine when promoting):**

- Support Agent / Sherpa integration is back online end to end in prod, now powered by real-time case event ingestion via Mulesoft.
- What changes for TSEs: Support Agent answers on new cases use freshly pre-warmed Sherpa context, so Next Best Action and Similar Cases land faster on case open.
- How to try it: open any new case in Salesforce; the Support Agent panel should populate within seconds.
- Where to ask: `#support-automation` for questions, feedback, or issue reports.

### Support Agent Usage Tracking Checklist for TSEs - Friday TSE Summary

- **Profile:** `weekly_support` (the weekly Friday CXI Team Updates email)
- **Target run:** Next Friday TSE Summary (earliest plausible: 2026-06-05).
- **Added:** 2026-05-29 by Kevin Baron-Quijano
- **Source:** [Confluence: Support Agent Usage Tracking Checklist for TSEs](https://databricks.atlassian.net/wiki/spaces/UN/pages/6391038145/Support+Agent+Usage+Tracking+Checklist+for+TSEs)
- **Promotion criterion:** Promote to Active once the TSE Summary draft for that Friday is being assembled.

**Content (draft, refine when promoting):**

- New TSE-facing reference page: [Support Agent Usage Tracking Checklist for TSEs](https://databricks.atlassian.net/wiki/spaces/UN/pages/6391038145/Support+Agent+Usage+Tracking+Checklist+for+TSEs) under the Metrics & observability tree.
- Covers Top Users vs Top Helpers, the FL scope filter, case-data-ingestion gaps (PLAT-162615 under investigation), supported environment, the new `Details` dashboard page for self-verification, schema-grant issues with `/support-agent:usage-supagent`, and how to escalate edge cases.
- Direct TSEs there before they DM individual CXI team members about missing usage.

## Consumed

(none yet)
