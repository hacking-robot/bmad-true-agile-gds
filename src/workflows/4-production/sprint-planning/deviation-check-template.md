# Deviation Detection Checklist

This template guides the deviation detection process during sprint planning. Use it to systematically compare planning documents against the actual codebase state.

## 1. Architecture Drift Detection

### File Structure Check
- [ ] Compare actual `src/` structure against architecture specs
- [ ] Verify folder organization matches documented structure
- [ ] Check for undocumented files/modules
- [ ] Check for missing files that should exist per architecture

### Dependencies Check
- [ ] Compare `package.json` dependencies against architecture decisions
- [ ] Verify selected frameworks/libraries are actually installed
- [ ] Check for undocumented dependencies added to project
- [ ] Verify version constraints match architecture specs

### Implementation Patterns Check
- [ ] Verify coding patterns match architectural decisions
- [ ] Check if error handling follows documented strategy
- [ ] Verify state management approach (if applicable)
- [ ] Check if module boundaries are respected

## 2. PRD Drift Detection

### Scope Alignment
- [ ] Compare actual project type against PRD classification
- [ ] Verify domain model matches PRD specifications
- [ ] Check if complexity level assessment still holds

### Feature Coverage
- [ ] Identify features implemented but not in PRD
- [ ] Identify PRD features not yet implemented
- [ ] Check if MVP scope boundaries are respected
- [ ] Verify post-MVP features haven't crept in prematurely

### Requirements Mapping
- [ ] Trace implemented code back to functional requirements
- [ ] Verify non-functional requirements are addressed
- [ ] Check for gold-plating (extra features not requested)

## 3. Incomplete FR Detection

### Previous Sprint Review
For each completed story in previous sprints:

- [ ] Load story file and identify covered FRs
- [ ] Scan codebase for FR implementation evidence
- [ ] Flag FRs that appear incomplete or missing
- [ ] Document specific gaps found

### FR Verification Checklist
- [ ] FR has corresponding code implementation
- [ ] FR has corresponding tests (if testable)
- [ ] FR behavior matches acceptance criteria
- [ ] Edge cases covered per FR specification

## Output Format

When drift is detected, format findings as:

```
### [Category] Drift Detected

| Expected | Actual | Impact |
|----------|--------|--------|
| [what doc says] | [what code shows] | [high/medium/low] |

Recommendation: [action to take]
```

## No Drift Found

If all checks pass:
```
✅ Deviation Check Passed

- Architecture: Aligned
- PRD: Aligned  
- Previous FRs: All implemented
```
