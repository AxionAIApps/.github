# AxionAIApps — Shared Workflows & Configuration

Organization-level reusable GitHub Actions workflows for all Axion AI Flutter apps.

## Reusable Workflows

### `flutter-build-deploy.yml`

Reusable CI/CD workflow that every Flutter app calls with a ~15-line caller.

**What it does:**
1. **Unit tests** with coverage gate (80% minimum, configurable)
2. **Build Android AAB** and upload to Play Store (internal track by default)
3. **Sync metadata** to Play Store via Fastlane
4. **Maestro smoke tests** (optional, non-blocking)

**Caller workflow example** (place in your app's `.github/workflows/build-deploy.yml`):

```yaml
name: Build & Deploy
on:
  workflow_dispatch:
    inputs:
      track:
        type: choice
        options: [internal, beta, production]
        default: internal
  push:
    branches: [main]
    paths: ['pubspec.yaml', 'lib/**', 'android/**', 'ios/**', 'test/**']

jobs:
  deploy:
    uses: AxionAIApps/.github/.github/workflows/flutter-build-deploy.yml@main
    with:
      app_name: yourapp
      bundle_id: com.axionaiapps.yourapp
      platform: android
      track: ${{ inputs.track || 'internal' }}
    secrets: inherit
```

## Secrets Convention

### Org-level (shared)
| Secret | Description |
|--------|-------------|
| `PLAY_SERVICE_ACCOUNT_JSON` | Google Play service account JSON |

### Per-app (repo-level)
Secret names use the app_name as prefix (case-insensitive):

| Secret | Example (healthbridge) |
|--------|----------------------|
| `{app_name}_KEYSTORE_BASE64` | `healthbridge_KEYSTORE_BASE64` |
| `{app_name}_STORE_PASSWORD` | `healthbridge_STORE_PASSWORD` |
| `{app_name}_KEY_PASSWORD` | `healthbridge_KEY_PASSWORD` |
| `{app_name}_KEY_ALIAS` | `healthbridge_KEY_ALIAS` |

## Inputs Reference

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `app_name` | yes | — | App name (lowercase) |
| `bundle_id` | yes | — | Android package name |
| `platform` | no | `android` | `android`, `ios`, or `both` |
| `track` | no | `internal` | Play Store track |
| `flutter_version` | no | `3.29.x` | Flutter SDK version |
| `coverage_threshold` | no | `80` | Minimum coverage % |
| `run_maestro` | no | `false` | Run Maestro smoke tests |
