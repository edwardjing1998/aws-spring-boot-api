name: RAPID Admin Main

on:
  push:
    branches:
    - rapid-admin-j
    
env:
  FORCE_COLOR: 2
  NODE: 16

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Clone repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 23.x

      - name: Install npm dependencies
        run: npm install

      - name: Run build
        run: npm run build



# This workflow warns and then closes issues and PRs that have had no activity for a specified amount of time.
#
# You can adjust the behavior by modifying this file.
# For more information, see:
# https://github.com/actions/stale
name: Mark stale issues and pull requests

on:
  schedule:
  - cron: '15 14 * * *'

jobs:
  stale:

    runs-on: ubuntu-latest
    permissions:
      issues: write
      pull-requests: write

    steps:
    - uses: actions/stale@v3
      with:
        repo-token: ${{ secrets.GITHUB_TOKEN }}
        stale-issue-message: 'This issue has been automatically marked as stale because it has not had recent activity. It will be closed if no further activity occurs. Thank you for your contributions'
        stale-pr-message: 'This PR has been automatically marked as stale because it has not had recent activity. It will be closed if no further activity occurs. Thank you for your contributions'
        stale-issue-label: 'no-issue-activity'
        stale-pr-label: 'no-pr-activity'



name: Mark stale issues and pull requests

on:
  push:               # ✅ Trigger when any commit is pushed
    branches:
      - main          # 🔁 Optional: specify your target branch
  workflow_dispatch:  # ✅ Optional: also allow manual trigger
        
