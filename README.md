# ApplePi Default Profiles

Default profile source for ApplePi users.

## Profiles

- `engineer` - software engineering profile for coding, debugging, reviews, and implementation planning.
- `data_analyst` - senior data analysis profile for dataset exploration, metric design, experiment review, business insight briefs, and executive reporting. Defaults Pi to `openai-codex` / `gpt-5.5`, and includes profile-bundled DeepWork analyst jobs plus a `/demos` skill for oil/healthcare demos.

## Data analyst DeepWork jobs

The `data_analyst` profile includes a DeepWork job bundle under:

```text
profiles/data_analyst/cli_specific/pi/deepwork/jobs/
```

When launched by an ApplePi version that supports profile-bundled DeepWork jobs, the selected profile contributes this folder to `DEEPWORK_ADDITIONAL_JOBS_FOLDERS` so DeepWork can discover the analyst job bundle. The bundle is self-contained in this profile; the analyst profile keeps `controls.pi.allow_external_deepwork_jobs` false so unrelated inherited job folders do not broaden the default analyst surface.

Included jobs and workflows:

- `analysis/ad_hoc_research_report` - scope research, answer data questions, incorporate Finder output, and produce a final report.
- `finder_analysis/run_finder` - run the Finder pattern-discovery pipeline and set up the project-local Finder binary at `utilities/finder/finder` when missing.
- `report_formatting/format_report` - turn a draft Markdown report into a polished DocBaker document or Slidev presentation.
- `datasource_management/onboard_datasource` - create query tooling and documentation for a new datasource.
- `datasource_management/explore_schema` - inspect and document datasource schema.
- `business_context/learn_about_business` - capture company, product, customer, and operating context.
- `analyst/analyze_dataset` - inspect data quality and produce evidence-backed findings.
- `analyst/define_metrics` - define product or business metrics, guardrails, and instrumentation acceptance criteria.
- `analyst/review_experiment` - assess experiment design or results without causal overclaiming.
- `analyst/insight_brief` - synthesize mixed evidence into a concise business recommendation.
- `analyst/executive_report` - create a leadership-ready analytical report.

## Data analyst `/demos` skill

The `data_analyst` profile also includes a profile-bundled Pi skill under:

```text
profiles/data_analyst/cli_specific/pi/skills/demos/
```

ApplePi versions that support profile-bundled Pi skills pass this folder to Pi as a `--skill` argument. The skill can fetch full runnable demo bundles from the legacy DeepWork Frontend R2-backed `/demo-bundle/<id>-full-data` route and run the self-contained analyst workflow for:

- `oil`
- `healthcare` / `medicaid-spending`

The skill is ordinary profile content. Users who do not want demo behavior can delete `cli_specific/pi/skills/demos/` from their local copied profile.

## Use with applepi setup

```bash
applepi setup
```

On a new machine will automatically use these profiles.

To bootstrap directly from this repository:

```bash
applepi setup https://github.com/applepi-ai/default-profiles
```

## Manual addition

Add this repository to your applepi settings:

```yaml
default_profile: engineer

profile_sources:
  - github: applepi-ai/default-profiles
    ref: main
    path: profiles
```

Then sync and run:

```bash
applepi sync
applepi run --profile engineer
applepi run --profile data_analyst
```

## Verify analyst jobs

After syncing profiles and using an ApplePi version with profile-bundled job support, run the `data_analyst` profile and ask DeepWork for available workflows. The bundled `analysis`, `business_context`, `datasource_management`, `finder_analysis`, `report_formatting`, and `analyst` jobs should appear with the workflows listed above.
