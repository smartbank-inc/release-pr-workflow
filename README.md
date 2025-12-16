# Create Release Pull Request Workflow Action

This is a [reusable GitHub Actions workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) that creates a pull request with [git-pr-release](https://github.com/x-motemen/git-pr-release).

Once this workflow creates a pull request, it notifies you on Slack.

## Usage

1. Create a Slack Webhook URL from https://api.slack.com/apps.
2. Set the Slack Webhook URL as a [GitHub Actions secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets).
3. Add a workflow configuration file as like the following examples into you repository.

That's it!

### Examples

#### Scheduled workflow

If you want to run the workflow at 04:00 UTC on every Monday, Tuesday, Wednesday, Thursday, and Friday, the following example works.

```yaml
name: release-pr

on:
  schedule:
    - cron: '0 4 * * 1,2,3,4,5'

jobs:
  call-workflow-passing-data:
    uses: smartbank-inc/release-pr-workflow/.github/workflows/release-pr.yml@main
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
      slack_webhook: ${{ secrets.SLACK_WEBHOOK }}
```

#### Manual trigger

If you want to [run the workflow manually](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow), the following example works.

```yaml
name: release-pr

on:
  workflow_dispatch:
    inputs:
      override:
        description: 'If you want to override the existing pull request, enter "true"'
        required: false
        default: ''

jobs:
  call-workflow-passing-data:
    uses: smartbank-inc/release-pr-workflow/.github/workflows/release-pr.yml@main
    with:
      override: ${{ github.event_name == 'workflow_dispatch' && github.event.inputs.override == 'true' }}
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
      slack_webhook: ${{ secrets.SLACK_WEBHOOK }}
```

![](https://i.imgur.com/diTKyPS.png)


### Inputs

| Name      | Required | Default               | Description                                      |
|-----------|----------|-----------------------|--------------------------------------------------|
| git_pr_release_branch_staging | `false`   | `release` | The branch name that the feature branches are merged into and is going to be merged into the "production" branch. |
| git_pr_release_branch_production | `false`   | `main` | The branch name that is deployed in production environment. |
| git_pr_release_template | `false`   | `.git-pr-release-template` | The template file path (relative to the workidir top) for pull requests created. Its first line is used for the PR title, the rest for the body. This is an ERB template. |
| git_pr_release_labels | `false`   | empty string | The labels list for adding to pull requests created. This value should be comma-separated strings. |
| git_pr_release_assign_pr_author | `false`   | `false` | Whether to assign the related users of the merged pull requests to the release pull request. |
| override | `false`   | `false` | If this value is true, this workflow overrides an existing branch and a pull request. If its' false, this workflow checks branch existence (If exists, stop to create a branch). |

### Secrets

| Name      | Required | Default               | Description                                      |
|-----------|----------|-----------------------|--------------------------------------------------|
| token | `true`   | - | GitHub token to be used for all git operations. |
| slack_webhook | `true`   | - | Slack Webhook URL. The URL is associated with a channel to be notified. |

