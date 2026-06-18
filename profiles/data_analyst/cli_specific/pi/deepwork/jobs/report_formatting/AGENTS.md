# report_formatting

Formats draft Markdown reports into polished Slidev presentations or DocBaker
HTML documents. Uses the "Presentation Expert" agent and follows a four-step
workflow: review inputs, typesetting, proofing (visual inspection via browser),
and response delivery.

Key conventions:
- Output format is determined by user notes: "presentation"/"deck" → Slidev; otherwise → DocBaker
- All numbers and charts must have citations (query file name or public URL)
- Component references and theme guidelines live in `.deepwork/learning-agents/presentation-expert/core-knowledge.md`
- Compiled output is opened directly in the browser for visual proofing

## Job Management

This folder and its subfolders are managed using `deepwork_jobs` workflows.

## Recommended Workflows

- `deepwork_jobs/new_job` - Full lifecycle: define → implement → test → iterate
- `deepwork_jobs/learn` - Improve instructions based on execution learnings
- `deepwork_jobs/repair` - Clean up and migrate from prior DeepWork versions

## Directory Structure

```
.
├── .deepreview        # Review rules for the job itself using Deepwork Reviews
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
