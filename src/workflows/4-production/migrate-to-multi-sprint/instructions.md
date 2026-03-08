# Migrate to True Agile (Just-In-Time Story Creation)

PURPOSE: Migrate an existing BMAD project from the old "all stories upfront" approach to true just-in-time agile where stories are created during sprint-planning based on capacity.

---

## Key Concepts

**OLD Approach (Model A):**
- create-epics creates ALL stories upfront
- sprint-planning SELECTS from existing stories
- backlog-refinement upgrades stories from placeholder→outlined→ready

**NEW Approach (Model B - True Agile):**
- create-epics creates EPIC CONTAINERS ONLY (no stories)
- sprint-planning CREATES stories based on target capacity
- Stories are created just-in-time, not pre-planned
- backlog-refinement workflow is DEPRECATED (no longer needed)

---

## Migration Strategy

### What We Keep

| Item | Action | Reason |
|------|--------|--------|
| **Done stories in epics.md** | Keep as-is | Historical record, proves epic progress |
| **Done story files** | Keep as-is | Contains completed work, decisions, useful history |
| **Undone stories in epics.md** | Keep as-is | Sprint-planning references them as context/suggestions |
| **Undone story files** | Archive to `archive/` | Preserve detailed planning, avoid conflicts with new creations |
| **sprint-status.yaml** | Archive | Replaced by sprint-N.yaml files |

### What We Don't Need

| Item | Reason |
|------|--------|
| **detail_level field** | No longer used - stories are either done or not done |
| **backlog-refinement references** | Workflow is deprecated |
| **Sprint history reconstruction** | Optional - only for velocity tracking |

---

## Migration Steps

### Step 1: Analyze Current State

```
🔍 Analyzing project structure...
```

1. Load epics.md (or sharded epic files)
2. Load sprint-status.yaml (if exists)
3. Load existing story files
4. Identify:
   - How many epics?
   - How many stories per epic?
   - Story status breakdown (done vs not done)
   - Any change proposals?

Display summary:

```
📊 Current Project State

Epics: 3
├── Epic 1: User Auth (5 stories: 4 done, 1 not done)
├── Epic 2: Content Management (6 stories: 2 done, 4 not done)
└── Epic 3: Search (3 stories: 0 done, 3 not done)

Total Stories: 14
├── Done: 6
└── Not Done: 8

Story Files: 14 files in implementation-artifacts/
Change Proposals: 2 files
```

### Step 2: Estimate Points for Done Stories

For DONE stories only, estimate story points based on complexity:

```
📝 Estimating Points for Done Stories

Epic 1: User Auth
├── Story 1.1: Login - 5 pts (API + UI + session management)
├── Story 1.2: Register - 5 pts (API + UI + validation)
├── Story 1.3: Profile - 3 pts (API + UI)
└── Story 1.4: Password Reset - 5 pts (API + email + UI)

Epic 2: Content Management
├── Story 2.1: Create Content - 5 pts
└── Story 2.2: List Content - 3 pts

Total done points: 26
```

Ask user: "Do these point estimates look reasonable? (Y/n/adjust)"

### Step 3: Update Epic Files

Update epics.md to new format:

**Changes:**
1. Add epic status field: `not-started` | `in-progress` | `done`
2. Add FR Coverage section (which requirements this epic covers)
3. Add Story Summary table with status column
4. Add points field to each story
5. Remove detail_level field (no longer used)

**Epic Status Rules:**
- All stories done → epic status: `done`
- Some stories done → epic status: `in-progress`
- No stories done → epic status: `not-started`

```
## Epic 1: User Authentication

Status: in-progress
Goal: Users can securely authenticate and manage their sessions

### FRs Covered
- FR-1: User login with email/password
- FR-2: User registration with email verification
- FR-3: Password reset flow
- FR-4: Session management

### Story Summary

| Key | Title | Points | Status |
|-----|-------|--------|--------|
| 1-1-user-login | User Login | 5 | done |
| 1-2-user-registration | User Registration | 5 | done |
| 1-3-user-profile | User Profile | 3 | done |
| 1-4-password-reset | Password Reset | 5 | done |
| 1-5-logout | Logout | 2 | not-done |

**Total Points:** 20 (18 done, 2 remaining)
```

### Step 4: Archive Undone Story Files

Move undone story files to archive to prevent conflicts:

```
📦 Archiving Undone Story Files

Moving to implementation-artifacts/archive/:
├── 1-5-logout.md (not done)
├── 2-3-update-content.md (not done)
├── 2-4-delete-content.md (not done)
├── 2-5-content-versioning.md (not done)
├── 2-6-content-search.md (not done)
├── 3-1-search-api.md (not done)
├── 3-2-search-ui.md (not done)
└── 3-3-search-filters.md (not done)

8 files archived. Original planning preserved for reference.
```

**Why archive?**
- Sprint-planning will CREATE fresh story files with potentially different structure
- Archived files preserve original planning context
- User can reference archived files during sprint-planning

### Step 5: Create Historical Sprint File (Optional)

If user wants velocity tracking:

```
📜 Creating Historical Sprint Record

Sprint 1 (completed):
├── Stories: 1-1, 1-2, 1-3, 1-4, 2-1, 2-2 (6 stories)
├── Points: 26
├── Status: completed
└── End Date: [ask user or use file dates]

This provides baseline velocity for future planning.
```

Create `sprints/sprint-1.yaml` with completed stories.

### Step 6: Create Velocity Log (Optional)

If historical sprint created:

```yaml
# sprints/velocity-log.yaml
sprints:
  - sprint_number: 1
    planned_points: 26
    completed_points: 26
    stories_completed: 6
    stories_carried: 0
    date_completed: [date]

averages:
  last_3_sprints: 26
  all_time: 26
  trend: "stable"
```

### Step 7: Archive Legacy Files

```
🗂️ Archiving Legacy Files

Archiving:
├── sprint-status.yaml → archive/sprint-status.yaml

Preserving:
├── Change proposals (not modified)
├── epics.md (updated in place)
├── Done story files (unchanged)
```

### Step 8: Finalize Migration

Display migration summary:

```
✅ Migration Complete!

📊 Summary:
├── Epics migrated: 3
├── Done stories preserved: 6 (26 points)
├── Not-done stories preserved: 8 (in epics.md for reference)
├── Story files archived: 8
├── Historical sprint created: Sprint 1 (26 pts)
└── Velocity log created: Yes

📋 Next Steps:
1. Review epic files to verify story status
2. Run `/bmad-gds-sprint-planning` to plan your next sprint
3. Sprint-planning will show not-done stories as "previously planned"
4. You can use, modify, or replace them based on current understanding

📁 Files Changed:
├── epics.md (updated with new format)
├── sprints/sprint-1.yaml (created)
├── sprints/velocity-log.yaml (created)
├── archive/ (8 story files moved)
└── archive/sprint-status.yaml (archived)
```

---

## How Sprint-Planning Uses Migrated Projects

When you run sprint-planning after migration:

1. **Agent loads epics.md** and finds undone stories
2. **Agent displays context:**
   ```
   📦 Epic 1: User Auth (status: in-progress)
   
   Previously planned stories (not yet done):
   ├── 1-5-logout: Logout (2 pts estimated)
   
   Remaining FRs to cover: FR-5 (Logout flow)
   ```
3. **You set capacity:** "I want 15 points this sprint"
4. **You select epics:** "Work on Epic 1 and start Epic 2"
5. **Agent proposes stories:**
   - Uses existing 1-5-logout as suggestion
   - Creates new stories for Epic 2 based on FRs and capacity
6. **You approve/adjust** the proposed stories
7. **Stories are created** and appended to epics.md

This preserves planning intelligence while enabling capacity-first approach.

---

## What Could Go Wrong

| Issue | Solution |
|-------|----------|
| Story points seem wrong | Update directly in epics.md before sprint-planning |
| Undone stories no longer relevant | Sprint-planning can create fresh, archive is just reference |
| Missing FR coverage | Add FRs to epic section in epics.md |
| Epic status incorrect | Update status field in epics.md |

---

## Files Modified

| File | Action |
|------|--------|
| `epics.md` | Updated in place (new format, status fields, points) |
| `sprints/sprint-1.yaml` | Created (historical sprint) |
| `sprints/velocity-log.yaml` | Created (velocity tracking) |
| `implementation-artifacts/archive/` | Created with archived story files |
| `sprint-status.yaml` | Archived |

---

## Verification

After migration:

1. ✅ Run `/bmad-gds-sprint-status` - should show epic progress
2. ✅ Check epics.md - should have status fields and story tables
3. ✅ Check archive/ - should contain undone story files
4. ✅ Run `/bmad-gds-sprint-planning` - should show undone stories as context
