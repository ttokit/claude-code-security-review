# Claude Code Security Review

AI-powered security review GitHub Action using Claude Code with OAuth authentication.

## Quick Start

Add this to your repository's `.github/workflows/security.yml`:

```yaml
name: Security Review

on:
  pull_request:
    types: [opened]

permissions:
  contents: read
  pull-requests: write
  id-token: write  # Required for OIDC authentication

jobs:
  security-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: ttokit/claude-code-security-review@oauth-v1.0.1
        with:
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

## Configuration

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `claude_code_oauth_token` | Claude Code OAuth token | Yes | - |
| `model` | Claude model to use | No | `claude-opus-4-5-20251101` |

### Using Sonnet (Pro Subscription)

If you have a Pro subscription and want to use Sonnet for cost efficiency:

```yaml
- uses: ttokit/claude-code-security-review@oauth-v1.0.1
  with:
    claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
    model: claude-sonnet-4-5-20250929
```

## Security Considerations

This action is not hardened against prompt injection attacks and should only be used to review trusted PRs. We recommend [configuring your repository](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-github-actions-settings-for-a-repository#controlling-changes-from-forks-to-workflows-in-public-repositories) to use the "Require approval for all external contributors" option to ensure workflows only run after a maintainer has reviewed the PR.

### SHA Pinning

For enhanced security, pin the action to a specific commit SHA instead of a tag:

```yaml
- uses: ttokit/claude-code-security-review@<COMMIT_SHA>  # oauth-v1.0.1
  with:
    claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

To get the SHA for a tag:

```bash
git ls-remote https://github.com/ttokit/claude-code-security-review.git refs/tags/oauth-v1.0.1
```

The trailing comment `# oauth-v1.0.1` enables Dependabot to detect version updates and create PRs automatically.

## License

MIT License - see [LICENSE](LICENSE) file for details.
