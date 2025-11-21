# Coldstart Prompt: Add Bootstrap Quickstart to README.md

**Priority**: P0 (Critical - Discoverability)
**Estimated Effort**: 30 minutes
**Generated**: 2025-11-21

---

## Context

AgentReady is a repository quality assessment tool that evaluates repositories against 25 agent-ready attributes. The `bootstrap` command (already implemented) sets up complete GitHub infrastructure in one command.

**Problem**: README.md currently has zero mentions of the bootstrap command. New users landing on GitHub only see `agentready assess` examples, missing the recommended entry point.

**Solution**: Add a dead-simple, copy-paste Bootstrap quickstart to README.md that positions bootstrap as the primary getting-started flow.

---

## Requirements from BACKLOG.md

### Description
Add a dead-simple, copy-paste Bootstrap quickstart to README.md. Currently, bootstrap documentation only exists in docs/user-guide.md, but README.md (the first thing users see on GitHub) has zero mentions of the bootstrap command.

### Problem
- New users landing on GitHub see only `agentready assess` commands
- Bootstrap command (the recommended entry point) is buried in documentation
- Missing the "most dead simple copy paste thing" for bootstrap

### Requirements
- Add prominent Bootstrap quickstart section to README.md
- 4-5 line copy-paste command sequence
- Show what gets created (bullets with checkmarks)
- Link to detailed tutorial in docs/user-guide.md
- Position ABOVE or alongside the assess quickstart

### Implementation

Add to README.md after Installation section:

```markdown
## Quick Start: Bootstrap (Recommended)

Transform your repository with one command:

```bash
cd /path/to/your/repo
agentready bootstrap .
git add . && git commit -m "build: Bootstrap agent-ready infrastructure"
git push
```

**What you get:**
- ✅ GitHub Actions workflows (tests, security, AgentReady assessment)
- ✅ Pre-commit hooks (formatters, linters)
- ✅ Issue/PR templates
- ✅ Dependabot configuration
- ✅ Automated assessment on every PR

**Duration**: <60 seconds

[See detailed Bootstrap tutorial →](docs/user-guide.md#bootstrap-your-repository)

---

## Quick Start: Assessment Only

For one-time analysis without infrastructure changes:

```bash
cd /path/to/your/repo
agentready assess .
open .agentready/report-latest.html
```
```

### Acceptance Criteria
- [ ] Bootstrap quickstart added to README.md
- [ ] Positioned prominently (before or alongside assess quickstart)
- [ ] Maximum 10 lines of code
- [ ] Shows clear value prop (what you get)
- [ ] Links to detailed docs
- [ ] Uses conventional commit message format

### Related
- Bootstrap command (#3 - already implemented)
- Documentation workflow
- User onboarding

### Notes
- Bootstrap is already implemented - this is just documentation
- README.md is the entry point for 90% of users
- This is blocking adoption - users don't know bootstrap exists
- Should match the style in docs/user-guide.md lines 87-115

---

## Implementation Checklist

### Step 1: Read Current README.md
- [ ] Read `/Users/jeder/repos/sk/agentready/README.md`
- [ ] Note the current Quick Start section (around line 14)
- [ ] Identify insertion point (after Installation, before current Quick Start)

### Step 2: Read Bootstrap Documentation
- [ ] Read `/Users/jeder/repos/sk/agentready/docs/user-guide.md` lines 87-115
- [ ] Note the style, tone, and structure of the existing bootstrap quickstart
- [ ] Ensure consistency with docs

### Step 3: Update README.md
- [ ] Add new section "## Quick Start: Bootstrap (Recommended)"
- [ ] Include 4-line command sequence
- [ ] Add bullet list showing what gets created
- [ ] Add duration indicator (<60 seconds)
- [ ] Link to detailed tutorial in docs/
- [ ] Rename existing Quick Start to "## Quick Start: Assessment Only"
- [ ] Add horizontal rule between sections

### Step 4: Verify Formatting
- [ ] Run `markdownlint README.md` to check formatting
- [ ] Fix any linting errors
- [ ] Ensure links work (test relative link to docs/user-guide.md)

### Step 5: Update Table of Contents (if exists)
- [ ] Check if README.md has a table of contents
- [ ] Add Bootstrap quickstart to TOC if present
- [ ] Update section numbering if needed

---

## Key Files to Review

Before starting implementation, review these files:

1. **README.md** (current state)
   - Location: `/Users/jeder/repos/sk/agentready/README.md`
   - Current Quick Start section around line 14
   - Installation section position

2. **docs/user-guide.md** (source of truth for bootstrap)
   - Location: `/Users/jeder/repos/sk/agentready/docs/user-guide.md`
   - Lines 87-115: Bootstrap quickstart section
   - Style guide for documentation

3. **BACKLOG.md** (original requirements)
   - Location: `/Users/jeder/repos/sk/agentready/BACKLOG.md`
   - Lines 165-237: Full backlog item

---

## Testing Strategy

### Manual Testing
1. **Markdown Rendering**
   - View README.md on GitHub (or use GitHub markdown preview)
   - Ensure checkmarks (✅) render correctly
   - Verify code blocks have proper syntax highlighting
   - Check that link to docs/user-guide.md works

2. **Link Verification**
   - Click link to docs/user-guide.md#bootstrap-your-repository
   - Ensure anchor link works correctly
   - Verify relative path is correct

3. **Copy-Paste Test**
   - Copy the command sequence
   - Paste into terminal (don't execute - just verify formatting)
   - Ensure no hidden characters or formatting issues

### Linting
```bash
# Run markdownlint on README.md
markdownlint README.md

# Fix any issues
markdownlint --fix README.md
```

---

## Success Criteria

**Functional:**
- [ ] Bootstrap quickstart is the first Quick Start section in README.md
- [ ] Command sequence is 4-5 lines maximum (copy-paste friendly)
- [ ] Bullet list shows clear value proposition
- [ ] Link to detailed docs works correctly
- [ ] Assessment quickstart is still present but secondary

**Quality:**
- [ ] Passes markdownlint validation
- [ ] Matches style and tone of docs/user-guide.md
- [ ] No broken links
- [ ] Checkmarks render correctly on GitHub
- [ ] Code blocks have proper bash syntax highlighting

**Adoption:**
- [ ] New users can immediately see bootstrap command
- [ ] Value prop is clear (what you get in <60 seconds)
- [ ] Path to detailed docs is obvious

---

## Getting Started

### Prerequisites
- AgentReady repository cloned locally
- Text editor or IDE
- markdownlint installed (`npm install -g markdownlint-cli`)

### Quick Start Commands

```bash
# Navigate to repository
cd /Users/jeder/repos/sk/agentready

# Read current README.md
cat README.md | head -50

# Read bootstrap docs for reference
cat docs/user-guide.md | sed -n '87,115p'

# Edit README.md
code README.md  # or vim, nano, etc.

# Run linter
markdownlint README.md

# Fix issues if any
markdownlint --fix README.md

# Preview changes
git diff README.md

# Commit changes
git add README.md
git commit -m "docs: Add bootstrap quickstart to README.md

- Add 'Quick Start: Bootstrap' section as primary entry point
- Move existing quick start to 'Quick Start: Assessment Only'
- Include 4-line command sequence for copy-paste
- Show value prop (what you get in <60 seconds)
- Link to detailed tutorial in docs/user-guide.md

Fixes #[issue-number]"
```

---

## Expected Diff Preview

```diff
--- a/README.md
+++ b/README.md
@@ -30,7 +30,31 @@
 pip install agentready
 ```

-## Quick Start
+## Quick Start: Bootstrap (Recommended)
+
+Transform your repository with one command:
+
+```bash
+cd /path/to/your/repo
+agentready bootstrap .
+git add . && git commit -m "build: Bootstrap agent-ready infrastructure"
+git push
+```
+
+**What you get:**
+- ✅ GitHub Actions workflows (tests, security, AgentReady assessment)
+- ✅ Pre-commit hooks (formatters, linters)
+- ✅ Issue/PR templates
+- ✅ Dependabot configuration
+- ✅ Automated assessment on every PR
+
+**Duration**: <60 seconds
+
+[See detailed Bootstrap tutorial →](docs/user-guide.md#bootstrap-your-repository)
+
+---
+
+## Quick Start: Assessment Only

 For one-time analysis without infrastructure changes:
```

---

## Notes

- Bootstrap command is already fully implemented - this is documentation only
- README.md is the #1 user entry point (90% of users see this first)
- This is blocking adoption - users don't discover bootstrap exists
- The docs/user-guide.md has excellent bootstrap documentation (lines 87-115)
- Match that style and tone for consistency

---

## Related Documentation

- **User Guide Bootstrap Section**: `docs/user-guide.md#bootstrap-your-repository`
- **Bootstrap Implementation**: `src/agentready/cli/bootstrap.py`
- **BACKLOG Item**: `BACKLOG.md` lines 165-237

---

**Last Updated**: 2025-11-21
**Status**: 🔴 Not Started
**Assignee**: Unassigned
