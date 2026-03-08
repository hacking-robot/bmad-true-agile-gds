# Jira Link - Manual Integration

<critical>The workflow execution engine is governed by: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {installed_path}/workflow.yaml</critical>
<critical>Communicate all responses in {communication_language}</critical>

<critical>
PURPOSE: Add Jira issue keys to BMAD epics and stories for manual tracking integration.

KEY CONCEPTS:
- This is MANUAL linking only - no automatic sync
- BMAD remains the source of truth for stories and epics
- Jira keys are stored as metadata references
- Useful for teams using Jira for tracking but BMAD for AI-assisted development
</critical>

<workflow>

<step n="1" goal="Display linking options">

<action>Load {project_context} for project-wide patterns (if exists)</action>
<action>Communicate in {communication_language} with {user_name}</action>

<output>
═══════════════════════════════════════════════════════════════
🔗 Jira Link Manager
═══════════════════════════════════════════════════════════════

This workflow adds Jira issue keys to BMAD epics and stories.

**What would you like to link?**

1. Link an Epic to Jira
2. Link a Story to Jira
3. View current Jira links
4. Exit

</output>

<ask>Select option (1-4):</ask>
</step>

<step n="2" goal="Handle selection">

<check if="user selects 1 - Link Epic">
  <action>List all epics from epic files</action>
  
  <output>
📁 **Available Epics:**

{{for each epic}}
  [{{epic_number}}] Epic {{epic_number}}: {{epic_title}}
      Current Jira: {{jira_key or 'not linked'}}
{{end}}

Which epic would you like to link? (enter epic number)
  </output>
  
  <ask>Epic number:</ask>
  <action>Set {{target_epic_num}} = user input</action>
  
  <output>
**Epic {{target_epic_num}}: {{epic_title}}**

Enter the Jira Epic Key (e.g., PROJ-100):
  </output>
  
  <ask>Jira Key:</ask>
  <action>Set {{jira_epic_key}} = user input</action>
  
  <action>Update epic file with jira_epic_key field</action>
  
  <output>
✅ Epic {{target_epic_num}} linked to {{jira_epic_key}}

Updated: {{epic_file_path}}
  </output>
  
  <action>Return to Step 1</action>
</check>

<check if="user selects 2 - Link Story">
  <action>Find active sprint</action>
  
  <check if="active sprint exists">
    <action>Load stories from active sprint</action>
    
    <output>
📋 **Stories in Sprint {{sprint_number}}:**

{{for each story}}
  [{{story_key}}] {{story_title}} ({{points}} pts)
      Current Jira: {{jira_key or 'not linked'}}
{{end}}

Which story would you like to link? (enter story key like "1-1")
    </output>
  </check>
  
  <check if="no active sprint">
    <action>Load all ready stories from epic files</action>
    
    <output>
📋 **Ready Stories (no active sprint):**

{{for each ready story}}
  [{{story_key}}] {{story_title}} ({{points}} pts) [Epic {{epic}}]
      Current Jira: {{jira_key or 'not linked'}}
{{end}}

Which story would you like to link? (enter story key like "1-1")
    </output>
  </check>
  
  <ask>Story key:</ask>
  <action>Set {{target_story_key}} = user input</action>
  
  <output>
**Story {{target_story_key}}: {{story_title}}**

Enter the Jira Issue Key (e.g., PROJ-101):
  </output>
  
  <ask>Jira Key:</ask>
  <action>Set {{jira_story_key}} = user input</action>
  
  <action>Update epic file story with jira_key field</action>
  <check if="active sprint exists">
    <action>Update sprint file story with jira_key field</action>
  </check>
  
  <output>
✅ Story {{target_story_key}} linked to {{jira_story_key}}

Updated: {{epic_file_path}}
{{if sprint_updated}}Updated: {{sprint_file_path}}{{end}}
  </output>
  
  <action>Return to Step 1</action>
</check>

<check if="user selects 3 - View Links">
  <action>Scan all epic files for jira_key fields</action>
  <action>Scan all sprint files for jira_key fields</action>
  
  <output>
📊 **Current Jira Links**

**Epics:**
{{for each epic with jira_key}}
  - Epic {{epic_number}}: {{epic_title}} → {{jira_key}}
{{end}}
{{if no epics linked}}  (none linked){{end}}

**Stories:**
{{for each story with jira_key}}
  - {{story_key}}: {{story_title}} → {{jira_key}}
{{end}}
{{if no stories linked}}  (none linked){{end}}
  </output>
  
  <action>Return to Step 1</action>
</check>

<check if="user selects 4 - Exit">
  <output>
✅ Jira link manager closed.

Remember: Jira links are references only. BMAD remains the source of truth for stories and epics.
  </output>
  <action>End workflow</action>
</check>

</step>

</workflow>

## Jira Key Format

- Epic keys: `PROJ-100`, `ABC-10`, etc. (typically multiples of 100)
- Story keys: `PROJ-101`, `ABC-11`, etc. (sequential)

## Storage Locations

- Epic Jira keys: Stored in epic file frontmatter as `jira_epic_key`
- Story Jira keys: 
  - Stored in epic file story section as `jira_key`
  - Also stored in sprint file (if story is in a sprint) as `jira_key`
