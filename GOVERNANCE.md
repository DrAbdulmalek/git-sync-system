# Governance Policy — git-sync-system

## Overview
This document defines the governance rules for the git-sync-system tool that manages 28+ GitHub repos and 11 HF Spaces.

## Sync Policies

### Default Policy: Read-Only
- All repos default to READ-ONLY mode
- Sync operations (push, delete branches) require explicit opt-in
- Pull/fetch operations are always allowed

### Write Modes
| Mode | Description | Requires |
|------|-------------|----------|
| read-only | Pull only, no pushes | Default |
| safe-push | Push to non-protected branches only | --write flag |
| full-write | Push to any branch including main | --force-write + confirmation |

## Allowlist / Denylist

### Allowlist (repos that CAN receive pushes)
- omni-medical-suite
- scanner-fixer
- medical-handwriting-ocr
- medical-ocr-training-hub
- medical-ocr-benchmarks
- medical-ocr-trainer
- profile-readme (DrAbdulmalek/DrAbdulmalek)

### Denylist (repos that MUST NEVER receive auto-pushes)
- ai-fuel-engine (archived)
- bilingual-extractor (archived)
- medical-doc-processor (archived)
- medical-ocr-postprocessor (archived)
- omniparse-study (archived)
- omniparse (archived)
- ponytail (archived)
- All repos with 'archived' status

### Sensitive Repos (require --force-write + explicit confirmation)
- medical-ocr-ground-truth (contains training data)
- Any repo with 'private' visibility

## Branch Protection Awareness

The system checks branch protection rules before pushing:
1. Query GitHub API for branch protection status
2. If branch is protected:
   - Require PR workflow instead of direct push
   - Warn user and require explicit confirmation
3. Never force-push to protected branches

## Audit Log

All sync operations are logged to `/home/z/my-project/git-sync-system/logs/audit.log`:
- Timestamp, operation type, repo, branch, result
- Push attempts (success/failure)
- Policy violations (denied writes to denylist repos)
- Manual overrides

## Configuration

Policies are configured in `config/governance.yaml`:

```yaml
default_mode: read-only
allowlist:
  - omni-medical-suite
  - scanner-fixer
  # ... (see above)
denylist:
  - ai-fuel-engine
  - bilingual-extractor
  # ... (see above)
sensitive:
  - medical-ocr-ground-truth
branch_protection:
  check_before_push: true
  deny_force_push_protected: true
audit:
  enabled: true
  log_file: logs/audit.log
  max_log_size_mb: 50
```
