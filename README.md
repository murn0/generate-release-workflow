# generate-release-workflow
Creating a pull request for release and the workflow to actually create the release when the pull request is merged are included. 

## Motivation
- Create a release on a pull request basis, not on a commit basis.
- Automatically update the semantic version using the labels included in the pull request.
- automatically add pull requests to milestones.
- Instead of creating a release directly, I would like to create a pull request and go through appropriate review and approval.

## Usage
### Create a pull request for release
``` yaml
uses: murn0/generate-release-workflow/.github/workflows/release-pull-request.yaml@3d4ed1b8620894318b7d43689cd3aeb2e999e8cb # v0.7.0
permissions:
  issues: write # For create milestone
  pull-requests: write # For pull requests to milestone
```

### Create a draft release when the pull request is merged
``` yaml
uses: murn0/generate-release-workflow/.github/workflows/release.yaml@3d4ed1b8620894318b7d43689cd3aeb2e999e8cb # v0.7.0
permissions:
  issues: write
if: |
  github.event.pull_request.merged == true && startsWith(github.event.pull_request.head.ref, 'release/v')
with:
  draft: true
```

## Settings
### release-pull-request.yaml
#### Inputs
| **Input** | **Required** | **Description** |
| --- | --- | --- |
| `changelog_config_json` | false | Configuration files specified in the [official](https://github.com/mikepenz/release-changelog-builder-action#configuration) |
| `major_title` | true | If the title set here is included in the changelog, it will be judged as a major update. |
| `minor_title` | true | If the title set here is included in the changelog, it will be judged as a minor update. |
| `reviewers` | false | Reviewers to be set for pull requests. |
| `labels` | false | Labels to be set for pull requests. |
| `app_id_url` | false | Specify a secret reference URL when reading the GithubApps AppID from the 1Password vault |
| `app_secret_url` | false | Specify a secret reference URL when reading the GithubApps AppSecret from the 1Password vault |
#### Secrets
| **Secret** | **Required** | **Description** |
| --- | --- | --- |
| `gh_app_id` | false | GithubApps AppID.<br>e.g. `${{ secrets.APP_ID }}` |
| `gh_app_private_key` | false | GithubApps AppSecret.<br>e.g. `${{ secrets.APP_SECRET }}` |
| `op_service_account_token` | false | 1Password service account token.<br>e.g. `${{ secrets.OP_SERVICE_ACCOUNT_TOKEN }}` |

### release.yaml
#### Inputs
| **Input** | **Required** | **Description** |
| --- | --- | --- |
| `draft` | false | If true, the release will be created as a draft. |
| `app_id_url` | false | Specify a secret reference URL when reading the GithubApps AppID from the 1Password vault |
| `app_secret_url` | false | Specify a secret reference URL when reading the GithubApps AppSecret from the 1Password vault |

#### Secrets
| **Secret** | **Required** | **Description** |
| --- | --- | --- |
| `gh_app_id` | false | GithubApps AppID.<br>e.g. `${{ secrets.APP_ID }}` |
| `gh_app_private_key` | false | GithubApps AppSecret.<br>e.g. `${{ secrets.APP_SECRET }}` |
| `op_service_account_token` | false | 1Password service account token.<br>e.g. `${{ secrets.OP_SERVICE_ACCOUNT_TOKEN }}` |

## Flow
### release-pull-request.yaml
1. Create changelog with [mikepenz/release-changelog-builder-action](https://github.com/mikepenz/release-changelog-builder-action)
2. Decision next version using `inputs.major_title` and `inputs.minor_title`
3. Check if a milestone with the same name as the next version exists, and create a new one if it does not exist. If it exists but is closed, re-open it and add the pull request included in the release to the milestone
4. Create a pull request for the release with the `release/<next-version-branch-name>`

### release.yaml
1. Create a release
2. Close the milestone

## Dependencies
- [actions/checkout](https://github.com/actions/checkout)
- [mikepenz/release-changelog-builder-action](https://github.com/mikepenz/release-changelog-builder-action)
- [aquaproj/aqua](https://github.com/aquaproj/aqua)
- [aquaproj/aqua-installer](https://github.com/aquaproj/aqua-installer)
- [tibdex/github-app-token](https://github.com/tibdex/github-app-token)
- [1password/load-secrets-action](https://github.com/1Password/load-secrets-action)
- [suzuki-shunsuke/actionlint-workflow](https://github.com/suzuki-shunsuke/actionlint-workflow)