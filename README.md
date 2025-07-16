# SV2 Integration Test Framework

This repository contains the integration tests for the SV2 protocol implementation across multiple repositories.

## Architecture

This framework tests the interaction between:
- **sv2-core-test** - Core protocols and utilities
- **sv2-pool-apps-test** - Pool and Job Declaration Server
- **sv2-miner-apps-test** - Job Declaration Client and Translator

## Reusable Workflow

This repository provides a reusable GitHub Actions workflow that other repositories can call to run integration tests:

```yaml
uses: GitGab19/sv2-integration-test-framework/.github/workflows/test-with-branch.yml@main
with:
  repo_name: "sv2-core-test"        # Repository being tested
  branch_name: ${{ github.head_ref }} # Branch to test
```

## Testing Strategy

### For Protocol Changes (sv2-core-test)
- Tests protocols with latest pool-apps and miner-apps
- Ensures backward compatibility

### For Pool Changes (sv2-pool-apps-test)  
- Tests pool apps with latest core protocols and miner-apps
- Validates pool-specific functionality

### For Miner Changes (sv2-miner-apps-test)
- Tests miner apps with latest core protocols and pool-apps
- Validates miner-specific functionality

## Local Testing

Each dependent repository has local scripts to run integration tests:

```bash
# From any dependent repo
./scripts/run-integration-tests.sh
```

## Test Contents

- `tests/` - Integration test scenarios
- `lib/` - Shared test utilities and helpers
- `mining_device/` - Test mining device implementation
- `mining_device_sv1/` - SV1 test mining device

## CI/CD

This repository runs tests with the latest main branches of all dependent repositories to ensure ongoing compatibility.
