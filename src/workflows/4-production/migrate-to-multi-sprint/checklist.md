# Migration Checklist

Use this checklist to verify the migration to true agile is complete and correct.

---

## Pre-Migration

- [ ] Epic files backed up
- [ ] sprint-status.yaml backed up
- [ ] Git status clean (or changes committed)
- [ ] User available to answer point estimation questions

---

## Epic File Updates

- [ ] Epic status field added: `not-started` | `in-progress` | `done`
- [ ] FRs Covered section added to each epic
- [ ] Story Summary table added to each epic section
- [ ] All DONE stories have `points: N` field
- [ ] All stories have `key: X-Y-slug` field
- [ ] All stories have `status: done | not-done` field
- [ ] detail_level field REMOVED (no longer used)
- [ ] Epic status matches story completion (all done → done, some done → in-progress)

---

## Story File Archiving

- [ ] `implementation-artifacts/archive/` directory created
- [ ] All NOT-DONE story files moved to archive/
- [ ] All DONE story files remain in implementation-artifacts/
- [ ] Archive preserves original filenames

---

## Sprint Files (Optional - for velocity tracking)

- [ ] `sprints/` directory created
- [ ] Historical sprint file created (if applicable)
- [ ] Sprint file has correct status (completed)
- [ ] Sprint dates populated
- [ ] All done stories from that sprint included
- [ ] Metrics calculated correctly (total_points, completed_points)

---

## Velocity Log (Optional)

- [ ] `sprints/velocity-log.yaml` created
- [ ] Sprint velocities calculated correctly
- [ ] Averages populated

---

## Legacy File Handling

- [ ] sprint-status.yaml archived (not deleted)
- [ ] Change proposal files preserved (not modified)

---

## Post-Migration Verification

- [ ] Run `/bmad-gds-sprint-status` - shows epic progress correctly
- [ ] Epic files render correctly (no broken formatting)
- [ ] Sprint files are valid YAML
- [ ] Archive directory contains correct files
- [ ] Sprint-planning shows not-done stories as context

---

## Understanding the New Approach

After migration, the workflow is:

1. **sprint-planning CREATES stories** based on capacity (not selects from backlog)
2. **Not-done stories in epics.md** are REFERENCE/SUGGESTIONS (not commitments)
3. **Archived story files** preserve original planning for context
4. **backlog-refinement workflow** is DEPRECATED (no longer exists)

When you run sprint-planning:
- Agent shows not-done stories as "previously planned"
- You can use them as-is, modify, or create fresh
- Stories are created to fit your capacity target

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Epic status wrong | Update status field in epics.md manually |
| Points seem wrong | Update directly in epics.md Story Summary table |
| Missing FRs | Add to FRs Covered section in epic |
| Story in wrong epic | Move row in Story Summary table |
| Want fresh stories | Sprint-planning can create new, archive is just reference |
