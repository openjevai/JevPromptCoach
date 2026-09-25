# OpenJEV support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the
original [TypeSafe](https://typesafe.ai) integration. TypeSafe remains the
default; anyone with a TypeSafe key sees zero behaviour change.

## What was added

- **`src/config.ts`** — `openjevApiKey()`, `jevProvider()`, `activeApiKey()`,
  and `apiKeySource()` updated to report the active provider's key source.
- **`src/jev.ts`** — `OPENJEV_MODEL`, `OPENJEV_BASE_URL`, `activeModel()`, and
  the `client()` function selects endpoint/model/key by provider.
- **`src/score.ts`** — uses `activeModel()` instead of the hardcoded `MODEL`
  constant so score records reflect the provider that answered.
- **`src/eval.ts`** — uses `activeModel()` and `activeApiKey()` for the key check.
- **`src/cli.ts`** — `requireKey()` and config/status output name the active
  provider's key and URL.
- **`README.md`** — OpenJEV note after the intro, alternative key setup, and
  updated data-flow diagram.

## Provider selection rule

1. **Explicit choice wins** — `JEV_PROVIDER=openjev` or `JEV_PROVIDER=typesafe`.
2. **Otherwise, TypeSafe if its key is set** (`TYPESAFE_API_KEY` — the unchanged default).
3. **Otherwise, OpenJEV if only `OPENJEV_API_KEY` is set**.

## Configuration

Add one of these to `~/.claude/jevpromptcoach/.env` (or export it in your shell):

```
# TypeSafe (default)
TYPESAFE_API_KEY=your-typesafe-key

# OpenJEV (optional alternative)
OPENJEV_API_KEY=your-openjev-key
# Optional: force OpenJEV even if a TypeSafe key is also set
# JEV_PROVIDER=openjev
```

Get an OpenJEV key at https://openjev.sh/dashboard.

## How it was verified

- A live `POST https://api.openjev.sh/v1/systemone` request with model `openjev`,
  state `ping`, one noul question — returned HTTP 200.
- `grep -r "api.typesafe.ai" src/` confirms no hardcoded TypeSafe default remains
  as a new endpoint; the SDK's own default still applies when TypeSafe is selected.

## Upstream

Original project: https://github.com/CrowdLinker/JevPromptCoach by @CrowdLinker.
