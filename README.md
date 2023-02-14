# syncpack-github-action

A GitHub Action to Synchronise monorepo dependency versions with [`syncpack`](https://github.com/zerodom30/syncpack).

## ⚠️ Project Status

[`syncpack`](https://github.com/zerodom30/syncpack) is stable but this GitHub Action is very new (May 15th 2022).

## 🌩 Installation

1. Create a [`.syncpackrc`](https://github.com/zerodom30/syncpack#-configuration-file) file in the root of your project.
   ```json
   {
     "dev": true,
     "filter": ".",
     "indent": "  ",
     "overrides": true,
     "peer": true,
     "prod": true,
     "resolutions": true,
     "workspace": true,
     "semverGroups": [],
     "semverRange": "",
     "sortAz": [
       "contributors",
       "dependencies",
       "devDependencies",
       "keywords",
       "peerDependencies",
       "resolutions",
       "scripts"
     ],
     "sortFirst": ["name", "description", "version", "author"],
     "source": [],
     "versionGroups": []
   }
   ```
1. Create a `/.github/workflows/syncpack.yml` workflow to run syncpack on Pull Requests.

   ```yaml
   on:
     # Run whenever a pull request is updated
     pull_request:
       branches:
         - main
   jobs:
     syncpack:
       runs-on: ubuntu-latest
       name: syncpack
       steps:
         # Check out the branch so we can read/write its files
         - uses: actions/checkout@v3
         # Use Node.js as syncpack is written in that
         - uses: actions/setup-node@v3
           with:
             node-version: 16
         - uses: zerodom30/syncpack-github-action@0.2.2
           env:
             # If any options are set to 'fix' then
             # env.GITHUB_TOKEN is needed to push to PRs
             GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   ```

1. Unless you don't plan to use `fix` to commit automatic fixes to your Pull Requests, go to `/settings/actions` in your repository and enable **Read and write permissions** under **Workflow permissions**.

## 🛠 Settings

All settings are optional, and their default values are listed below.

```yaml
with:
  # Email to be used when the GitHub Action Commits and Comments
  bot-email: 'github-actions@github.com'
  # GitHub Username to be used when the GitHub Action Commits and
  # Comments
  bot-username: 'github-actions[bot]'
  # Commit message to be used when auto-fixing PRs
  commit-message: 'chore(syncpack): format and sync package.json files'
  # How to handle unconventional formatting/ordering of package.json
  # files
  #
  # * 'fix' - Auto-fix and commit to the PR
  # * 'ignore' - Do nothing
  format-mismatches: 'fix'
  # Whether to update package-lock.json, pnpm-lock.yaml, or yarn.lock
  # when semver range or version mismatches have been auto-fixed
  #
  # * 'fix' - Auto-fix and commit to the PR
  # * 'ignore' - Do nothing
  lockfile-mismatches: 'fix'
  # The specific version of your chosen `package-manager`
  package-manager-version: 'latest'
  # What to use to update your lockfile
  #
  # * 'npm'
  # * 'pnpm'
  # * 'yarn'
  package-manager: 'npm'
  # Ensure dependency versions follow a consistent format
  #
  # * 'lint' - Block merging the PR until manually fixed
  # * 'fix' - Auto-fix and commit to the PR
  # * 'ignore' - Do nothing
  semver-range-mismatches: 'fix'
  # Migrate to a newer version of syncpack than was available at the
  # time of writing this GitHub action
  syncpack-version: '7.2.1'
  # How to handle dependencies which are required by multiple packages,
  # where the version is not the same across every package
  #
  # * 'lint' - Block merging the PR until manually fixed
  # * 'fix' - Auto-fix and commit to the PR
  # * 'ignore' - Do nothing
  version-mismatches: 'fix'
```
