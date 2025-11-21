# Coldstart Prompt: GitHub Pages Linter Integration

**Priority**: P2 (Medium - Quality)
**Estimated Effort**: 2-3 hours
**Generated**: 2025-11-21

---

## Context

AgentReady has a GitHub Pages documentation site in the `docs/` directory using Jekyll. The site includes:
- User guide, developer guide, API reference
- Strategic roadmaps documentation
- Attributes documentation
- Examples

**Current State:**
- GitHub Pages site configured in `docs/_config.yml`
- Repository: `ambient-code/agentready`
- GitHub Pages URL: https://ambient-code.github.io/agentready/
- Existing CI/CD: `.github/workflows/tests.yml`, `security.yml`, `agentready-assessment.yml`, `update-docs.yml`

**Problem:** No automated linting for Jekyll/GitHub Pages content before deployment. Need to catch issues like:
- Broken internal links
- Invalid Jekyll frontmatter
- Missing required fields
- Liquid syntax errors
- Markdown syntax issues

---

## Objective

Find an appropriate linter for GitHub Pages/Jekyll sites and integrate it into the repository's existing CI/CD automation.

---

## Requirements

### 1. Research GitHub Pages Linters

Evaluate and select the best linter for this use case. Consider:

**Popular Options:**
- **htmlproofer** (Ruby) - Validates HTML, checks links, images
- **markdown-link-check** (Node.js) - Checks Markdown links
- **jekyll build --strict** - Built-in Jekyll validation
- **actionlint** - GitHub Actions workflow linter
- **markdownlint-cli** (already used for markdown files)

**Selection Criteria:**
- Compatible with Jekyll/GitHub Pages
- Checks internal and external links
- Validates frontmatter/metadata
- Fast execution (<2 min for our docs)
- Good GitHub Actions support
- Active maintenance

### 2. Integration with Existing Automation

**Existing Workflows to Consider:**
- `.github/workflows/tests.yml` - Runs on PR and push to main
- `.github/workflows/update-docs.yml` - Documentation updates
- `.github/workflows/security.yml` - Security scanning
- `.github/workflows/agentready-assessment.yml` - Repository assessment

**Integration Options:**

**Option A: Add to existing `tests.yml`**
- Pro: Single workflow, faster feedback
- Con: Slower overall test run

**Option B: Create new `docs-lint.yml`**
- Pro: Separate concerns, parallel execution
- Con: One more workflow to maintain

**Option C: Add to `update-docs.yml`**
- Pro: Runs when docs change
- Con: Only triggered by workflow_dispatch

**Recommendation:** Create new `docs-lint.yml` that runs on:
- Pull requests (any file in `docs/`)
- Pushes to main (any file in `docs/`)
- Manual dispatch

### 3. Linting Scope

**What to Lint:**
- All Markdown files in `docs/`
- Jekyll configuration (`docs/_config.yml`)
- Liquid templates in `docs/_layouts/` (if any)
- Internal links between documentation pages
- External links to major sources (Anthropic, Microsoft, etc.)

**What NOT to Lint:**
- Source code (already linted by black, isort, ruff)
- Generated files (`.agentready/`)
- Repomix outputs

### 4. Error Handling

**Failure Behavior:**
- Broken internal links → FAIL (block PR)
- Invalid frontmatter → FAIL (block PR)
- Broken external links → WARN (don't block, just notify)
- Missing alt text on images → WARN

### 5. Configuration

Create `.github/workflows/docs-lint.yml` with:
- Triggers on PR/push to `docs/**` paths
- Install linter dependencies
- Run linter with appropriate flags
- Upload results as artifact
- Comment on PR with findings (if any)

---

## Implementation Checklist

### Step 1: Research Linters
- [ ] Review htmlproofer documentation
- [ ] Review markdown-link-check documentation
- [ ] Test `jekyll build --strict` locally
- [ ] Compare performance and features
- [ ] Select best option for our use case

### Step 2: Test Locally
- [ ] Install selected linter locally
- [ ] Run on `docs/` directory
- [ ] Identify any existing issues
- [ ] Fix critical issues (broken links, invalid frontmatter)
- [ ] Document configuration options

### Step 3: Create GitHub Actions Workflow
- [ ] Create `.github/workflows/docs-lint.yml`
- [ ] Configure triggers (PR, push to main, workflow_dispatch)
- [ ] Add path filters (only run when `docs/**` changes)
- [ ] Install linter in workflow
- [ ] Run linter with appropriate flags
- [ ] Upload results as artifact

### Step 4: Configure Linter
- [ ] Create linter config file (if needed)
- [ ] Ignore external link checks or make them warnings
- [ ] Configure frontmatter validation
- [ ] Set up link checking
- [ ] Define success/failure criteria

### Step 5: Add PR Comments
- [ ] Use GitHub Actions to comment on PR with results
- [ ] Show summary of issues found
- [ ] Link to full linter output artifact
- [ ] Only comment if issues found

### Step 6: Documentation
- [ ] Update `CLAUDE.md` with linting workflow
- [ ] Add section to developer guide about docs linting
- [ ] Document how to run linter locally
- [ ] Add troubleshooting for common issues

### Step 7: Test & Validate
- [ ] Create test PR with intentional docs issues
- [ ] Verify workflow triggers correctly
- [ ] Verify issues are caught and reported
- [ ] Verify PR comments appear
- [ ] Fix any workflow bugs

---

## Key Files to Review

### Existing Workflows
- `.github/workflows/tests.yml` - Current test automation
- `.github/workflows/update-docs.yml` - Docs update workflow
- `.github/workflows/security.yml` - Security scanning pattern
- `.github/workflows/agentready-assessment.yml` - Assessment pattern

### Documentation Structure
- `docs/_config.yml` - Jekyll configuration
- `docs/index.md` - Homepage
- `docs/user-guide.md` - User documentation
- `docs/developer-guide.md` - Developer documentation
- `docs/roadmaps.md` - Strategic roadmaps
- `docs/attributes.md` - Attribute documentation
- `docs/api-reference.md` - API reference
- `docs/examples.md` - Examples

### Project Configuration
- `CLAUDE.md` - Development workflow documentation (update with linting info)
- `.gitignore` - Ensure linter outputs are ignored

---

## Recommended Implementation: htmlproofer

**Why htmlproofer:**
- Industry standard for Jekyll sites
- Checks HTML output (catches more issues than markdown-only linters)
- Built-in link checking (internal and external)
- Image validation
- GitHub Actions support
- Active maintenance

**Workflow Example:**

```yaml
name: Documentation Linting

on:
  pull_request:
    paths:
      - 'docs/**'
      - '.github/workflows/docs-lint.yml'
  push:
    branches: [main]
    paths:
      - 'docs/**'
  workflow_dispatch:

jobs:
  lint-docs:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.2'
          bundler-cache: true

      - name: Install Jekyll and dependencies
        run: |
          cd docs
          bundle install

      - name: Build Jekyll site
        run: |
          cd docs
          bundle exec jekyll build --strict

      - name: Install htmlproofer
        run: gem install html-proofer

      - name: Run htmlproofer
        run: |
          htmlproofer docs/_site \
            --disable-external \
            --allow-hash-href \
            --check-html \
            --check-img-http \
            --enforce-https \
            --report-invalid-tags \
            --report-missing-names \
            --log-level :info

      - name: Upload results
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: htmlproofer-results
          path: tmp/.htmlproofer/

      - name: Comment on PR
        if: failure() && github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '⚠️ Documentation linting failed. See workflow logs for details.'
            })
```

**Configuration File (`docs/Gemfile`):**

```ruby
source 'https://rubygems.org'

gem 'jekyll', '~> 4.3'
gem 'jekyll-feed'
gem 'jekyll-seo-tag'
gem 'jekyll-sitemap'
gem 'html-proofer'

group :jekyll_plugins do
  gem 'jekyll-feed'
  gem 'jekyll-seo-tag'
  gem 'jekyll-sitemap'
end
```

---

## Alternative: markdown-link-check

**Why markdown-link-check:**
- Lighter weight (Node.js, not Ruby)
- Faster execution
- Simpler setup
- Good for link-only validation

**Workflow Example:**

```yaml
name: Documentation Link Check

on:
  pull_request:
    paths:
      - 'docs/**'
  push:
    branches: [main]
    paths:
      - 'docs/**'
  workflow_dispatch:

jobs:
  link-check:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install markdown-link-check
        run: npm install -g markdown-link-check

      - name: Check links in documentation
        run: |
          find docs -name '*.md' -print0 | \
            xargs -0 -n1 markdown-link-check \
              --config .markdown-link-check.json

      - name: Comment on PR
        if: failure() && github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '⚠️ Broken links found in documentation. See workflow logs for details.'
            })
```

**Configuration (`.markdown-link-check.json`):**

```json
{
  "ignorePatterns": [
    {
      "pattern": "^http://localhost"
    }
  ],
  "timeout": "20s",
  "retryOn429": true,
  "retryCount": 3,
  "fallbackRetryDelay": "30s",
  "aliveStatusCodes": [200, 206, 301, 302, 308]
}
```

---

## Testing Strategy

### Unit Testing (Local)
```bash
# Install linter
gem install html-proofer  # or npm install -g markdown-link-check

# Test on docs directory
cd docs
bundle exec jekyll build --strict
htmlproofer _site --disable-external

# Fix any issues found
# Re-run until clean
```

### Integration Testing (CI)
1. Create test PR with intentional issues:
   - Broken internal link
   - Invalid frontmatter
   - Missing image
2. Verify workflow catches issues
3. Fix issues
4. Verify workflow passes

### Manual Verification
- [ ] Workflow runs on PR creation
- [ ] Workflow runs on push to main
- [ ] Workflow can be manually triggered
- [ ] Results are uploaded as artifacts
- [ ] PR comments appear when issues found
- [ ] Workflow passes when docs are clean

---

## Success Criteria

**Functional:**
- [ ] Linter integrated into GitHub Actions
- [ ] Runs automatically on PRs touching docs
- [ ] Catches broken internal links
- [ ] Validates Jekyll frontmatter
- [ ] Reports issues clearly in workflow logs
- [ ] Comments on PR when issues found

**Quality:**
- [ ] Fast execution (<2 min for our docs)
- [ ] No false positives on valid links
- [ ] Clear error messages
- [ ] Easy to run locally

**Documentation:**
- [ ] Developer guide updated with linting info
- [ ] Local testing instructions provided
- [ ] Troubleshooting guide for common issues
- [ ] CLAUDE.md updated with workflow info

---

## Getting Started

### Quick Start Commands

```bash
# Navigate to repository
cd /Users/jeder/repos/sk/agentready

# Research linters (choose one)
# Option 1: htmlproofer
gem install html-proofer
cd docs && bundle install && bundle exec jekyll build --strict
htmlproofer _site --disable-external

# Option 2: markdown-link-check
npm install -g markdown-link-check
find docs -name '*.md' -exec markdown-link-check {} \;

# Create workflow file
code .github/workflows/docs-lint.yml

# Test locally
# Fix any issues found

# Create test PR
git checkout -b add-docs-linter
git add .github/workflows/docs-lint.yml
git commit -m "ci: Add GitHub Pages linter to CI/CD"
git push -u origin add-docs-linter

# Create PR and verify workflow runs
gh pr create --title "Add documentation linting to CI/CD" --body "Adds automated linting for GitHub Pages documentation"
```

---

## Notes

- GitHub Pages builds Jekyll automatically, but we want to catch issues earlier (in CI)
- Consider rate limiting for external link checks (GitHub Actions has limited outbound bandwidth)
- htmlproofer can be slow on large sites - may want to cache build artifacts
- markdown-link-check is faster but less comprehensive
- Can run both: markdown-link-check for speed, htmlproofer for comprehensive checks
- Add badge to README.md showing docs linting status

---

## Related Documentation

- **Jekyll Documentation**: https://jekyllrb.com/docs/
- **htmlproofer**: https://github.com/gjtorikian/html-proofer
- **markdown-link-check**: https://github.com/tcort/markdown-link-check
- **GitHub Pages**: https://docs.github.com/en/pages
- **GitHub Actions**: https://docs.github.com/en/actions

---

## Related Issues

- Bootstrap Command (#3) - Created GitHub Actions workflows
- Report improvements (#4, #5) - Documentation that needs linting

---

**Last Updated**: 2025-11-21
**Status**: 🔴 Not Started
**Assignee**: Unassigned
**Labels**: ci/cd, documentation, quality
