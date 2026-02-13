# Datadog Synthetics CI Setup Guide

## Summary of Fixes

The Datadog Synthetics CI workflow has been fixed and is now properly configured. The following issues were resolved:

### Issues Fixed ✅

1. **Invalid Action Version**: Changed from `@v1` (which doesn't exist) to `@v3.8.2` (latest stable)
2. **Incorrect Parameter Names**: Updated from underscore format (`api_key`, `app_key`) to hyphen format (`api-key`, `app-key`) as required by v3.x
3. **Removed Deprecated Parameters**: Removed `poll_results` which is no longer needed in v3.x
4. **Missing Documentation**: Added comprehensive setup instructions
5. **Graceful Failure Handling**: Workflow now skips execution if secrets aren't configured instead of failing

## Current Status

The workflow file (`.github/workflows/datadog-synthetics.yml`) is now syntactically correct and will:
- ✅ Skip gracefully if Datadog API secrets are not configured
- ✅ Use the correct action version and parameters
- ✅ Not fail when no synthetic tests are found
- ✅ Run on push, pull requests, and hourly schedule (if secrets are configured)

## Next Steps to Complete Setup

To make the Datadog Synthetics monitoring fully operational, follow these steps:

### 1. Configure GitHub Secrets

Add the following secrets to your GitHub repository settings:

```
Settings → Secrets and variables → Actions → New repository secret
```

Required secrets:
- `DATADOG_API_KEY`: Your Datadog API key
- `DATADOG_APP_KEY`: Your Datadog Application key

To get these keys:
1. Log in to [Datadog](https://app.datadoghq.com)
2. Go to Organization Settings → API Keys
3. Create or copy your API key
4. Go to Organization Settings → Application Keys
5. Create or copy your Application key

### 2. Create Synthetic Tests in Datadog

You have two options:

#### Option A: Using the Datadog UI (Recommended for first setup)

1. Go to [Datadog Synthetic Tests](https://app.datadoghq.com/synthetics/tests)
2. Click "New Test" → "Browser Test" or "API Test"
3. Configure your test to monitor `https://asperbeautyshop.lovable.app`
4. Example tests to create:
   - Homepage loads successfully
   - Product page is accessible
   - Shopping cart functionality
   - Search functionality
5. After creating tests, note their public IDs (e.g., `abc-d3f-ghi`)

#### Option B: Using Configuration Files

Create a file named `tests.synthetics.json` in your repository:

```json
{
  "tests": [
    {
      "id": "your-test-public-id-here",
      "config": {
        "startUrl": "https://asperbeautyshop.lovable.app"
      }
    }
  ]
}
```

### 3. Update Workflow (Optional)

If you want to specify which tests to run explicitly, uncomment and update the `public-ids` parameter in `.github/workflows/datadog-synthetics.yml`:

```yaml
public-ids: |
  abc-d3f-ghi
  jkl-mn0-pqr
```

### 4. Test the Workflow

Once secrets and tests are configured:
1. Push a commit or create a pull request
2. Check the Actions tab in GitHub
3. The "Datadog Synthetics CI" workflow should now run successfully

## Workflow Behavior

### Without Secrets Configured
- Workflow will skip execution (not fail)
- Status: ⏭️ Skipped

### With Secrets but No Tests
- Workflow will run successfully but report 0 tests executed
- Status: ✅ Success

### With Secrets and Tests
- Workflow will run the configured synthetic tests
- Status: ✅ Success (if tests pass) or ❌ Failure (if tests fail)

## Schedule

The workflow runs:
- On every push to `main` or `develop` branches
- On every pull request to `main` or `develop` branches
- Hourly (via cron schedule)

To modify the schedule, edit the `cron` expression in the workflow file.

## Troubleshooting

### Workflow Still Failing?

1. **Check secrets are set**: Verify `DATADOG_API_KEY` and `DATADOG_APP_KEY` in repository settings
2. **Verify API key permissions**: Ensure your Datadog API key has Synthetics Read/Write permissions
3. **Check test IDs**: If using `public-ids`, ensure the test IDs are correct and tests exist in Datadog
4. **Review logs**: Check the workflow run logs in GitHub Actions for specific error messages

### Common Errors

- `Authentication failed`: Check your API/App keys are correct
- `Tests not found`: Verify test IDs or create tests in Datadog dashboard
- `Rate limit exceeded`: Reduce the schedule frequency or contact Datadog support

## Resources

- [Datadog Synthetics CI Documentation](https://docs.datadoghq.com/continuous_testing/cicd_integrations/)
- [GitHub Action Repository](https://github.com/DataDog/synthetics-ci-github-action)
- [Datadog Synthetics Tests](https://app.datadoghq.com/synthetics/tests)
- [API Key Management](https://docs.datadoghq.com/account_management/api-app-keys/)

## Questions?

If you need help with setup, refer to the [Datadog documentation](https://docs.datadoghq.com/synthetics/) or contact your Datadog support team.
