# Sprint Planning - True Agile (Capacity-First)

<critical>The workflow execution engine is governed by: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/_bmad/gds/workflows/4-production/sprint-planning/workflow.yaml</critical>
<critical>Communicate all responses in {communication_language}</critical>

## 📚 Overview

This workflow CREATES stories for a sprint based on capacity. Key features:
- **Capacity-first** - Set target points, stories created to fit
- **Just-in-time stories** - Stories created during planning, not pre-planned
- **Cross-epic selection** - Work on any epic(s) you choose
- **Story creation** - Agent proposes stories sized to fit capacity

**This is TRUE AGILE:** Stories are created when needed, not upfront.

<workflow>

<step n="1" goal="Check for Active Sprint">

<action>Load {project_context} for project-wide patterns and conventions (if exists)</action>
<action>Communicate in {communication_language} with {user_name}</action>

<action>Check for existing sprint files in {implementation_artifacts}/sprints/</action>
<action>Look for any sprint-N.yaml file with status: active</action>

<check if="active sprint found">
  <action>Load the active sprint file</action>
  
  <output>
⚠️ **Active Sprint Detected**

Sprint {{active_sprint_number}} is currently active.

**Sprint {{active_sprint_number}} Status:**
- Started: {{start_date}}
- Target End: {{target_end}}
- Stories: {{story_count}} ({{completed_count}} done)
- Points: {{completed_points}}/{{total_points}}

You must close the current sprint before creating a new one.

**Options:**
1. View sprint status
2. Close sprint (run sprint-review)
3. Cancel
  </output>
  
  <ask>What would you like to do?</ask>
  
  <check if="user selects 2">
    <output>
💡 Run `/bmad-bmm-sprint-review` to close Sprint {{active_sprint_number}} first.
    </output>
    <action>End workflow</action>
  </check>
  
  <check if="user selects 3">
    <action>End workflow</action>
  </check>
</check>

<check if="no active sprint">
  <action>Proceed to Step 2</action>
</check>

</step>

<step n="2" goal="Load Velocity History and Set Capacity">

<action>Load {velocity_log} if exists</action>
<action>Load all epic files from {planning_artifacts}</action>

<check if="velocity history exists">
  <action>Calculate average velocity from last 3 sprints</action>
  <action>Set {{recommended_capacity}} = average velocity</action>
  
  <output>
📊 **Velocity History:**

| Sprint | Planned | Completed | Velocity |
|--------|---------|-----------|----------|
{{for each historical sprint}}
| {{sprint_number}} | {{planned_points}} | {{completed_points}} | {{velocity}} |

**Averages:**
- Last 3 sprints: {{avg_last_3}} pts
- All time: {{avg_all_time}} pts
- Trend: {{trend}}

💡 **Recommended capacity:** {{recommended_capacity}} points
  </output>
</check>

<check if="no velocity history">
  <output>
📊 **Velocity History:** No previous sprints

💡 This is your first sprint! Consider starting with a conservative capacity (e.g., 10-15 points).
  </output>
  
  <action>Set {{recommended_capacity}} = null</action>
</check>

<ask>What is your target sprint capacity? (points)</ask>

<action>Set {{target_capacity}} from user response</action>

<action>Proceed to Step 3</action>

</step>

<step n="3" goal="Select Epics for Sprint">

<action>Display all epics from {planning_artifacts}</action>
<action>For each epic, show: status, remaining FRs, previously planned undone stories (if any)</action>

<output>
📦 **Available Epics:**

| Epic | Title | Status | Remaining Work |
|------|-------|--------|----------------|
{{for each epic}}
| {{epic_number}} | {{epic_title}} | {{epic_status}} | {{remaining_description}} |

**Previously Planned Stories (not yet done):**
{{for each epic with undone stories}}
- Epic {{epic_number}}: {{undone_story_titles}}
{{end}}

💡 These undone stories are suggestions. You can use them, modify them, or create fresh stories.
</output>

<ask>Which epic(s) do you want to work on this sprint? (Enter epic numbers, comma-separated)</ask>

<action>Parse user response into {{selected_epics}} array</action>

<check if="multiple epics selected">
  <ask>How would you like to allocate {{target_capacity}} points across these epics?</ask>
  <output>
Options:
1. Equal split ({{points_per_epic}} pts each)
2. Custom allocation
  </output>
  
  <check if="user selects custom">
    <ask>Enter point allocation for each epic (e.g., "1:15, 2:10"):</ask>
    <action>Parse into {{epic_allocations}} map</action>
  </check>
  
  <check if="user selects equal">
    <action>Set {{epic_allocations}} = equal split</action>
  </check>
</check>

<check if="single epic selected">
  <action>Set {{epic_allocations}} = full capacity to selected epic</action>
</check>

<action>Proceed to Step 4</action>

</step>

<step n="4" goal="Detect Planning Deviations">

<action>Load PRD from {planning_artifacts}</action>
<action>Load Architecture from {planning_artifacts}</action>
<action>Scan actual project codebase structure</action>

<action>Compare Architecture specifications against actual codebase:
  - File/folder structure vs architecture specs
  - Dependencies in package.json vs architecture decisions
  - Implementation patterns vs architectural decisions
</action>

<check if="architecture drift detected">
  <output>
──────────────────────────────────────────────────────────────
⚠️ **ARCHITECTURE DRIFT DETECTED**

The codebase differs from the Architecture document:

| Expected (Architecture) | Actual (Codebase) |
|-------------------------|-------------------|
{{for each drift item}}
| {{expected}} | {{actual}} |
{{end}}

❗ **Recommendation:** Cancel planning and run `/bmad-bmm-correct-course` to reconcile the architecture with reality before planning new work.
──────────────────────────────────────────────────────────────
  </output>
  
  <ask>How would you like to proceed?
1. Cancel planning (recommended)
2. Proceed aware of the drift</ask>
  
  <check if="user selects 1">
    <action>End workflow</action>
  </check>
</check>

<action>Compare PRD specifications against actual codebase:
  - Project scope alignment
  - Feature set coverage
  - Any code that doesn't map to PRD requirements
</action>

<check if="PRD drift detected">
  <output>
──────────────────────────────────────────────────────────────
⚠️ **PRD DRIFT DETECTED**

The codebase differs from the PRD document:

{{for each drift item}}
- {{drift_description}}
{{end}}

❗ **Recommendation:** Cancel planning and run `/bmad-bmm-correct-course` to reconcile the PRD with reality before planning new work.
──────────────────────────────────────────────────────────────
  </output>
  
  <ask>How would you like to proceed?
1. Cancel planning (recommended)
2. Proceed aware of the drift</ask>
  
  <check if="user selects 1">
    <action>End workflow</action>
  </check>
</check>

<action>Scan completed stories from previous sprints for potentially incomplete FRs:
  - Review stories marked as "done"
  - Check if all FRs covered by those stories are actually implemented
</action>

<check if="incomplete FRs found">
  <output>
──────────────────────────────────────────────────────────────
📋 **POTENTIALLY INCOMPLETE FRs DETECTED**

These FRs were in stories marked "done" but may not be fully implemented:

| FR | Description | From Story |
|----|-------------|------------|
{{for each incomplete FR}}
| {{fr_id}} | {{fr_description}} | {{story_key}} |
{{end}}

You may want to include these in the new sprint to ensure completion.
──────────────────────────────────────────────────────────────
  </output>
  
  <ask>Include these incomplete FRs in sprint planning? (Y/n)</ask>
  
  <check if="user says yes">
    <action>Add incomplete FRs to {{remaining_frs}} for story creation</action>
  </check>
</check>

<check if="no drift detected">
  <output>
✅ **Deviation Check Passed**

No significant drift detected between planning documents and codebase.
  </output>
</check>

<action>Proceed to Step 5</action>

</step>

<step n="5" goal="Create Stories for Each Epic">

<action>For each epic in {{selected_epics}}:</action>
<action>Load epic file, PRD, Architecture</action>
<action>Identify FRs not yet covered by done stories</action>
<action>Check for previously planned undone stories (from migration or earlier sprints)</action>

<output>
──────────────────────────────────────────────────────────────
📝 **Creating Stories for Epic {{epic_number}}** ({{allocated_points}} pts allocated)

**Epic:** {{epic_title}}
**Goal:** {{epic_goal}}
**FRs to cover:** {{remaining_frs}}

**Previously Planned (undone):**
{{for each undone story}}
- {{story_key}}: {{story_title}} ({{story_points}} pts) - can use/modify/replace
{{end}}
</output>

<action>Analyze FRs and epic scope</action>
<action>Propose stories that:
  - Cover remaining FRs
  - Fit within allocated points
  - Are appropriately sized (ideally 3-8 pts each)
  - Can be done by single developer
  - Have clear acceptance criteria
</action>

<output>
**Proposed Stories:**

┌─────────────────────────────────────────────────────────────┐
{{for each proposed story}}
│ Story {{story_key}}: {{story_title}}
│ Points: {{story_points}}
│ Covers: {{frs_covered}}
│ 
│ Acceptance Criteria:
{{for each AC}}
│ - Given {{context}}, When {{action}}, Then {{result}}
{{end}}
└─────────────────────────────────────────────────────────────┘
{{end}}

**Total for Epic {{epic_number}}:** {{epic_total_points}} pts
</output>

<ask>Accept these stories for Epic {{epic_number}}? (Y/n/adjust)</ask>

<check if="user says adjust">
  <ask>What would you like to adjust? (split/merge/remove/add/describe change)</ask>
  <action>Iterate on story proposals until user accepts</action>
</check>

<check if="user accepts">
  <action>Mark stories as approved for this epic</action>
  <action>Store in {{approved_stories}} array</action>
</check>

</action>

<action>Repeat for all selected epics</action>

<action>Proceed to Step 6</action>

</step>

<step n="6" goal="Review Sprint Plan">

<action>Calculate total points from {{approved_stories}}</action>

<output>
═══════════════════════════════════════════════════════════════
📋 **Sprint Plan Summary**
═══════════════════════════════════════════════════════════════

**Target Capacity:** {{target_capacity}} pts
**Planned Stories:** {{total_points}} pts
**Variance:** {{variance}} pts

──────────────────────────────────────────────────────────────
**Stories by Epic:**

{{for each epic}}
📦 **Epic {{epic_number}}: {{epic_title}}** ({{epic_points}} pts)
{{for each story in epic}}
   ├── {{story_key}}: {{story_title}} ({{story_points}} pts)
{{end}}
{{end}}

──────────────────────────────────────────────────────────────
**All Stories:**

| Key | Title | Points | Epic |
|-----|-------|--------|------|
{{for each story}}
| {{story_key}} | {{story_title}} | {{story_points}} | {{epic_number}} |
{{end}}

**Total:** {{total_points}} points
</output>

<check if="total_points exceeds target_capacity significantly (>)">
  <output>
⚠️ **Warning:** Planned points ({{total_points}}) exceed target capacity ({{target_capacity}}) by {{overage}} points.

Consider removing some stories or increasing capacity.
  </output>
</check>

<ask>Does this sprint plan look good? (Y/n)</ask>

<check if="user says no">
  <output>
Options:
1. Remove stories
2. Add stories  
3. Adjust capacity
4. Cancel and start over
  </output>
  <action>Handle user choice and iterate</action>
</check>

<check if="user says yes">
  <action>Proceed to Step 7</action>
</check>

</step>

<step n="7" goal="Set Sprint Dates and Team">

<action>Get current date as {{start_date}}</action>

<ask>What is the target end date for this sprint? (e.g., "2024-01-19" or "2 weeks")</ask>

<action>Parse user response into {{target_end}} date</action>

<ask>Who is on the sprint team? (comma-separated names, or press Enter to skip)</ask>

<action>Parse into {{team_members}} array</action>

<action>Proceed to Step 8</action>

</step>

<step n="8" goal="Create Sprint File and Update Epics">

<action>Determine next sprint number from existing sprint files</action>
<action>Set {{sprint_number}}</action>

<action>Create sprint file at {sprints_dir}/sprint-{{sprint_number}}.yaml</action>

<output>
📝 Creating Sprint {{sprint_number}} file...
</output>

<action>Write sprint file with:
  - sprint_number
  - status: active
  - dates: start, target_end
  - team_members
  - planned_stories (with epic linkage, points, status: planned)
  - metrics: total_points, completed_points: 0
</action>

<action>For each story in {{approved_stories}}:</action>
<action>APPEND story to epic file in {planning_artifacts}/epics.md</action>
<action>Update Story Summary table in epic section</action>
<action>Update epic status (not-started → in-progress if first story)</action>

<output>
📝 Appending stories to epic files...
</output>

<critical>🔄 INVOKE CREATE-STORY WORKFLOW FOR EACH STORY</critical>
<action>For each story in {{approved_stories}}:</action>
<action>Invoke create-story workflow with mode="create" and story_key</action>
<note>The create-story workflow will create comprehensive story files with full context analysis</note>
<note>This ensures all stories have the same high-quality context regardless of how they're initiated</note>

<output>
📝 Creating story files via create-story workflow...
</output>

<output>
═══════════════════════════════════════════════════════════════
✅ **Sprint {{sprint_number}} Created!**
═══════════════════════════════════════════════════════════════

**Sprint Details:**
- Sprint Number: {{sprint_number}}
- Status: active
- Start Date: {{start_date}}
- Target End: {{target_end}}
- Team: {{team_members or "Not specified"}}

**Stories:** {{story_count}} stories, {{total_points}} points

**Files Created:**
- {sprints_dir}/sprint-{{sprint_number}}.yaml
- {{for each story}} {implementation_artifacts}/{{story_key}}.md

**Files Updated:**
- {planning_artifacts}/epics.md (stories appended)

──────────────────────────────────────────────────────────────
**Next Steps:**

1. Run `/bmad-bmm-sprint-status` to view sprint progress
2. Run `/bmad-bmm-dev-story` to start development on a story
3. When sprint complete, run `/bmad-bmm-sprint-review`
</output>

</step>

</workflow>

---

## Story Points Guide

| Points | Complexity | Time Rough Guide |
|--------|------------|------------------|
| 1 | Trivial | < 2 hours |
| 2 | Simple | 2-4 hours |
| 3 | Small | 4-8 hours (half day) |
| 5 | Medium | 1-2 days |
| 8 | Large | 2-4 days |
| 13 | Very Large | 4-5 days (consider splitting) |
| 21 | Epic-like | 1+ week (must split) |

---

## Story Creation Principles

1. **Single developer** - Each story can be done by one person
2. **Clear scope** - Well-defined boundaries
3. **Testable** - Has acceptance criteria
4. **Valuable** - Delivers user value
5. **Sized right** - 3-8 points ideal, split if > 13

---

## Epic Status Updates

When stories are created and appended to epic files:

| Condition | Epic Status |
|-----------|-------------|
| No stories done yet | `not-started` |
| Some stories done, more remaining | `in-progress` |
| All stories done | `done` |

---

## Files Referenced

| File | Purpose |
|------|---------|
| `{sprints_dir}/sprint-N.yaml` | Sprint definition |
| `{planning_artifacts}/epics.md` | Epic containers + stories |
| `{implementation_artifacts}/*.md` | Story detail files |
| `{velocity_log}` | Velocity history |
