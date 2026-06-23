# Engagement Harness Configuration Reference

Generated for **Financial ** / Pilot .
Edit `config.json` — this file is documentation only.

---

## agents.enabled

Which agents run on every PR. Remove agents you don't need to reduce cost and noise.

| Agent | What it checks | Enabled | Provider |
|-------|---------------|---------|----------|
| reviewer | General code quality and best practices | ✓ | `anthropic` |
| security | Secret scanning, hardcoded creds, injection risks | ✓ | `anthropic` |
| domain-policy | Engagement-specific rules from rules/ | ✓ | `anthropic` |
| testing | Test coverage gaps and test quality | ✓ | `anthropic` |
| data-architecture | Schema changes, migrations, data model concerns | ✓ | `anthropic` |
| sre-observability | Logging, metrics, error handling, alerting | ✓ | `anthropic` |
| design-principles | SOLID, DRY, architecture patterns | ✓ | `anthropic` |
| pr-intent-gap | Intent vs implementation drift detection | ✓ | `anthropic` |
| remediation | Auto-generates fix suggestions | ✓ | `anthropic` |

---

## models

Maps each agent to an AI provider.

| Value | Description | Cost/PR (est.) |
|-------|-------------|----------------|
| `anthropic` | Claude (recommended) | ~$0.005 |
| `openai` | GPT-4 | ~$0.010 |
| `mock` | No findings (testing only) | Free |

---

## review.confidenceThreshold

**Range:** 0.0 to 1.0  ·  **Default:** 0.8

Findings below this threshold are filtered out before reporting.

- Raise to 0.9 if seeing too many false positives
- Lower to 0.6 if missing real issues
- Check with: `engagement-harness feedback pilot-report`

---

## review.severityThreshold

**Default:** low  ·  **Valid values:** low · medium · high · critical

Only report findings at or above this severity. Start with `low` and raise after tuning.

---

## ci.postComments

**Default:** true

Posts each finding as an inline PR comment.
Requires `pull-requests: write` permission in workflows.
Enables reaction-based feedback collection (👍 👎 🚀 😕).

---

## ci.blockOnPolicy

**Default:** false

Fails the GitHub check if any findings exist.
**Recommendation:** Enable after 2 weeks of tuning when false positive rate is below 10%.

---

## ci.artifactsOnly

**Default:** true

Uploads the full report as a GitHub Actions artifact.
Set to `false` if you only want inline comments and no artifact upload.

---

## providers.anthropic / providers.openai

Configures the AI provider used by agents mapped to that provider.

```json
{
  "model": "claude-sonnet-4-6",
  "maxTokens": 4096,
  "temperature": 0.0
}
```

**Anthropic models:**
| Model | Quality | Cost |
|-------|---------|------|
| claude-opus-4-8 | Most capable | Highest |
| claude-sonnet-4-6 | Recommended | Best value |
| claude-haiku-4-5-20251001 | Fastest | Lowest |

**OpenAI models:**
| Model | Quality | Cost |
|-------|---------|------|
| gpt-4-turbo | Recommended | Best balance |
| gpt-4o | Faster | Good quality |
| gpt-3.5-turbo | Cheapest | Less accurate |

---

## pipeline (review.confidenceThreshold)

Confidence filtering is controlled via `review.confidenceThreshold` (see above).

---

## feedback.enabled

**Default:** true

Enables feedback collection workflows. When true, the `feedback-on-merge.yml` workflow
collects 👍/👎 reactions from PR comments to measure review accuracy over time.

---

## feedback.autoCollect

**Default:** false

Automatically runs the weekly feedback sweep. Set to `true` once your team has been
using the tool for 2+ weeks.

---

## feedback.retentionDays

**Default:** 90

How many days to keep raw feedback data before pruning.

---

## context.maxFiles

**Default:** 30

Maximum number of changed files to include in the review context.
Raise if large PRs are being partially reviewed.

---

## context.maxTokens

**Default:** 80000

Maximum tokens sent to each agent per review run.
Lower if hitting provider rate limits.

---

## context.ignoredPaths

Glob patterns for files to exclude from review context.
Example: `["**/node_modules/**", "**/*.lock", "dist/**"]`

---

## alm.platform

**Valid values:** github · gitlab · azure-devops · bitbucket · none

Determines which ALM API to use for posting inline PR comments.

---

## reports.formats

**Default:** json · markdown · html

Which report formats to generate after each review run.

---

## reports.outputDir

**Default:** .engagement-harness/reports

Where to write report files. This path is gitignored by default.
