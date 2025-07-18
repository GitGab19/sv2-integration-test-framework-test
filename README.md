# SV2 Integration Test Framework

This repository contains the integration tests for the SV2 protocol implementation across multiple repositories. It provides **automated PR testing** to ensure changes in any dependent repository don't break the overall system integration.

## Architecture

This framework tests the interaction between:
- **sv2-core-test** - Core protocols and utilities
- **sv2-pool-apps-test** - Pool and Job Declaration Server
- **sv2-miner-apps-test** - Job Declaration Client and Translator

## 🚀 Automated PR Testing

### Overview
Whenever a PR is opened on any of the three dependent repositories, this framework automatically runs comprehensive integration tests using the PR's code. This ensures:

- **Early Detection**: Integration issues are caught before merging
- **Cross-Repository Validation**: Changes in one repo are tested against the entire ecosystem  
- **Continuous Integration**: Maintains system stability across all components

### How It Works

1. **PR Trigger**: When a PR is opened/updated in any dependent repo
2. **Code Checkout**: The framework checks out the PR branch
3. **Dependency Patching**: Updates local dependencies to use the PR code
4. **Test Execution**: Runs all integration tests with the modified code
5. **Results Reporting**: Comments on the PR with test results

## 📋 Setup Instructions

### For Repository Maintainers

To enable automatic integration testing for PRs in your repository, add the appropriate workflow file to your repository's `.github/workflows/` directory:

#### For sv2-core-test Repository
Create `.github/workflows/integration-tests.yml`:
```yaml
name: Integration Tests for sv2-core-test PR

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches: [main, develop]

jobs:
  integration-tests:
    name: Run Integration Tests
    uses: GitGab19/sv2-integration-test-framework-test/.github/workflows/test-with-branch.yml@main
    with:
      repo_name: "sv2-core-test"
      branch_name: ${{ github.head_ref }}
      repo_url: ${{ github.event.pull_request.head.repo.html_url }}
      repo_slug: ${{ github.event.pull_request.head.repo.full_name }}
      pr_number: ${{ github.event.number }}
    secrets: inherit
```

#### For sv2-pool-apps-test Repository
Create `.github/workflows/integration-tests.yml`:
```yaml
name: Integration Tests for sv2-pool-apps-test PR

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches: [main, develop]

jobs:
  integration-tests:
    name: Run Integration Tests
    uses: GitGab19/sv2-integration-test-framework-test/.github/workflows/test-with-branch.yml@main
    with:
      repo_name: "sv2-pool-apps-test"
      branch_name: ${{ github.head_ref }}
      repo_url: ${{ github.event.pull_request.head.repo.html_url }}
      repo_slug: ${{ github.event.pull_request.head.repo.full_name }}
      pr_number: ${{ github.event.number }}
    secrets: inherit
```

#### For sv2-miner-apps-test Repository
Create `.github/workflows/integration-tests.yml`:
```yaml
name: Integration Tests for sv2-miner-apps-test PR

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches: [main, develop]

jobs:
  integration-tests:
    name: Run Integration Tests
    uses: GitGab19/sv2-integration-test-framework-test/.github/workflows/test-with-branch.yml@main
    with:
      repo_name: "sv2-miner-apps-test"
      branch_name: ${{ github.head_ref }}
      repo_url: ${{ github.event.pull_request.head.repo.html_url }}
      repo_slug: ${{ github.event.pull_request.head.repo.full_name }}
      pr_number: ${{ github.event.number }}
    secrets: inherit
```

> **💡 Tip**: Sample workflow files are provided in this repository under `.github/workflows/pr-integration-tests-*.yml` for easy copying.

### Quick Installation

For automatic setup, you can use the installation script:

```bash
# Download and run the installer from your repository root
curl -fsSL https://raw.githubusercontent.com/GitGab19/sv2-integration-test-framework-test/main/install-pr-workflow.sh | bash

# Or clone this repository and run the script locally
git clone https://github.com/GitGab19/sv2-integration-test-framework-test.git
cd your-sv2-repository
../sv2-integration-test-framework-test/install-pr-workflow.sh
```

The script will:
- Automatically detect your repository type
- Create the appropriate workflow file
- Provide next steps for activation

### Permissions Required

The workflow requires the following permissions in the target repository:
- `contents: read` - To checkout the PR code
- `actions: read` - To call the reusable workflow
- `pull-requests: write` - (Optional) To comment test results on PRs

## 🔧 Reusable Workflow API

This repository provides a reusable GitHub Actions workflow that other repositories can call:

### Workflow: `test-with-branch.yml`

**Inputs:**
- `repo_name` (required): Repository name (`sv2-core-test`, `sv2-pool-apps-test`, or `sv2-miner-apps-test`)
- `branch_name` (required): Branch name to test
- `repo_url` (required): Repository URL (supports forks)
- `repo_slug` (required): Repository slug (e.g., `GitGab19/sv2-core-test`)
- `pr_number` (optional): PR number for better logging

**Example Usage:**
```yaml
uses: GitGab19/sv2-integration-test-framework-test/.github/workflows/test-with-branch.yml@main
with:
  repo_name: "sv2-core-test"
  branch_name: ${{ github.head_ref }}
  repo_url: ${{ github.event.pull_request.head.repo.html_url }}
  repo_slug: ${{ github.event.pull_request.head.repo.full_name }}
  pr_number: ${{ github.event.number }}
```

## 🧪 Testing Strategy

### For Protocol Changes (sv2-core-test)
- Tests all core protocols with latest pool-apps and miner-apps
- Ensures backward compatibility across the ecosystem
- Validates protocol changes don't break higher-level applications

### For Pool Changes (sv2-pool-apps-test)  
- Tests pool applications with latest core protocols and miner-apps
- Validates pool-specific functionality and integration points
- Ensures mining pools work correctly with protocol changes

### For Miner Changes (sv2-miner-apps-test)
- Tests miner applications with latest core protocols and pool-apps
- Validates miner-specific functionality and translation capabilities
- Ensures miners can connect and operate with pools correctly

## 🏃 Local Testing

Each dependent repository includes local scripts to run integration tests during development:

```bash
# From any dependent repo
./scripts/run-integration-tests.sh
```

For manual testing with this framework:
```bash
# Clone this repository
git clone https://github.com/GitGab19/sv2-integration-test-framework-test.git
cd sv2-integration-test-framework-test

# Run tests with default dependencies (latest main branches)
cargo test --features sv1

# Run tests with specific branch/repository
# (requires manual Cargo.toml modification)
```

## 📁 Repository Structure

```
├── tests/                          # Integration test scenarios
│   ├── jd_integration.rs           # Job Declaration integration tests
│   ├── pool_integration.rs         # Pool integration tests
│   ├── translator_integration.rs   # Translator integration tests
│   └── ...                         # Additional test scenarios
├── lib/                            # Shared test utilities and helpers
│   ├── interceptor.rs             # Network message interception
│   ├── message_aggregator.rs      # Message collection and validation
│   ├── mock_roles.rs              # Mock SV2 role implementations
│   └── ...                        # Additional utilities
├── .github/workflows/
│   ├── test-with-branch.yml       # Main reusable workflow
│   ├── ci.yml                     # Continuous integration
│   ├── pr-integration-tests-*.yml # Sample PR workflows for copying
│   └── ...
└── template-provider/             # Test template provider setup
```

## 🔄 CI/CD Integration

### This Repository
- **Main CI**: Runs tests with latest main branches of all dependent repositories
- **PR Testing**: Tests changes to the integration framework itself
- **Workflow Validation**: Ensures reusable workflows function correctly

### Dependent Repositories
- **PR Integration**: Automatically runs integration tests for all PRs
- **Cross-Repository Testing**: Changes are validated against the entire ecosystem
- **Result Reporting**: Test results are commented directly on PRs

## 🛠️ Advanced Configuration

### Custom Test Subsets
The workflow supports running specific test subsets by modifying the cargo test command:

```yaml
# In your PR workflow, you can customize test execution:
- name: Run specific tests
  run: |
    cd integration-tests
    cargo test --features sv1 --test jd_integration -- --nocapture
```

### Fork Support
The workflow automatically handles testing PRs from forks by using the provided `repo_url` and `repo_slug` parameters.

### Timeout Configuration
Tests have a 45-minute timeout to handle complex integration scenarios. This can be adjusted in the workflow file if needed.

## 🐛 Troubleshooting

### Common Issues

1. **Dependency Resolution Failures**
   - Ensure the PR branch is up-to-date with the main branch
   - Check that all required dependencies are properly exported in the target repository

2. **Test Timeouts**
   - Individual tests have a 30-minute timeout
   - Large integration tests may require optimization or splitting

3. **Permission Errors**
   - Ensure the target repository has the required permissions for calling external workflows
   - Check that the repository has access to the integration test framework

### Debug Information
The workflow provides extensive logging including:
- Repository and branch information
- Dependency patching details
- Test execution progress
- Error diagnostics

## 📞 Support

For issues related to:
- **Integration Test Framework**: Open an issue in this repository
- **PR Workflow Setup**: Check the sample workflows and setup instructions above
- **Test Failures**: Review the workflow run logs for detailed error information

---

*This framework ensures robust integration testing across the SV2 ecosystem, providing confidence in cross-repository changes and maintaining system stability.*
