# Job Management

This folder and its subfolders are managed using `deepwork_jobs` workflows.

## Recommended Workflows

- `deepwork_jobs/new_job` - Full lifecycle: define → implement → test → iterate
- `deepwork_jobs/learn` - Improve instructions based on execution learnings
- `deepwork_jobs/repair` - Clean up and migrate from prior DeepWork versions

## Directory Structure

```
.
├── AGENTS.md          # This file - project context and guidance
├── job.yml            # Job specification (created by define step)
├── steps/             # Step instruction files (created by implement step)
│   └── *.md           # One file per step
├── hooks/             # Custom validation scripts and prompts
│   └── *.md|*.sh      # Hook files referenced in job.yml
├── scripts/           # Reusable scripts and utilities created during job execution
│   └── *.sh|*.py      # Helper scripts referenced in step instructions
└── templates/         # Example file formats and templates
    └── *.md|*.yml     # Templates referenced in step instructions
```

## Editing Guidelines

1. **Use workflows** for structural changes (adding steps, modifying job.yml)
2. **Direct edits** are fine for minor instruction tweaks
3. **Run `deepwork_jobs/learn`** after executing job steps to capture improvements
4. **Run `deepwork install`** after any changes to regenerate commands

## Job-Specific Context

### define_report

#### Learnings from 2026-02-17 run (customer_churn_report)

- **Align step rarely needed for recurring reports**: The report specification serves as the alignment document, so the five-phase pattern typically reduces to four steps (Prepare, Analyze, Present, Feedback) for recurring reports. The create_workflow step now documents this.
- **Step instruction redundancy with common_job_info**: The new_job implement step produced step instructions that duplicated query tool paths, directory structures, and conventions already in common_job_info. This was caught in review but cost a revision cycle. The create_workflow step now warns about this pitfall.
- **Analyze step completeness**: The analyze step of the generated workflow missed several analyses on first submission (geographic/income, CLTV median, ARPU segmented, adoption rates, new customer profile). This was addressed by adding a pre-submission completeness checklist to the customer_analytics analyze step — see `.deepwork/jobs/customer_analytics/steps/analyze_churn.md`.
- **business_context/overview.md may not exist**: The add_actioning step tries to read this file for business context. When it's absent, follow-up automation proposals are limited. Step now handles this gracefully.

#### Reference files
- Report specification pattern: See `docs/reports/customer_analytics/customer_churn_report/report_specification.md` for a complete example
- Best practices: See `research_report_job_best_practices.md` in this directory
- First completed job: See `.deepwork/jobs/customer_analytics/job.yml` (v1.1.0)
- Test results: See `.deepwork/tmp/test_feedback.md` for the customer_churn_report test run results
