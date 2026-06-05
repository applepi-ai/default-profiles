# ApplePi Default Profiles

Default profile source for ApplePi users.

## Profiles

- `engineer` - software engineering profile for coding, debugging, reviews, and implementation planning.
- `data_analyst` - data analysis profile for dataset exploration, statistical reasoning, and reporting.

## Use with applepi setup

```bash
applepi setup
```
On a new machine will automatically use these profiles.

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
