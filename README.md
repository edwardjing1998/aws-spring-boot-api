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



PS C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin\actions-runner> curl -o actions-runner-linux-x64-2.317.0.tar.gz -L -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkZzekx4enBtR1A5cVlCNUpfaVdaNVFwaERzdyJ9.eyJuYW1laWQiOiJkZGRkZGRkZC1kZGRkLWRkZGQtZGRkZC1kZGRkZGRkZGRkZGQiLCJzY3AiOiJBY3Rpb25zUnVudGltZS5QYWNrYWdlRG93bmxvYWQiLCJJZGVudGl0eVR5cGVDbGFpbSI6IlN5c3RlbTpTZXJ2aWNlSWRlbnRpdHkiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9zaWQiOiJERERERERERC1ERERELUREREQtRERERC1EREREREREREREREQiLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3ByaW1hcnlzaWQiOiJkZGRkZGRkZC1kZGRkLWRkZGQtZGRkZC1kZGRkZGRkZGRkZGQiLCJhdWkiOiJlN2Y4NDIxYS00YjdkLTRiOTAtODQxNS0yOWY1ZDBlOTVhNzUiLCJzaWQiOiI2OTg4ZTZiZi0zYTEzLTRlNmMtOGY1Yy1jYTc0YjA1Mjg4MTQiLCJpc3MiOiJnaXRodWIub25lZmlzZXJ2Lm5ldCIsImF1ZCI6ImdpdGh1Yi5vbmVmaXNlcnYubmV0L19zZXJ2aWNlcy92c3Rva2VuIiwibmJmIjoxNzUwMDQzMTMxLCJleHAiOjE3NTAwNDczMzF9.m3Ggo7UazQJ53Y_I6rPM6bpZOMcaxUs9aFY6pLx6CInUqS4iG9xGGEvUcPlsa4E06dSjXgmHrG-LzOSQ-hPVm9ZVDeDpYLd2onWJaxZFsDJGxFB7yAfmvlPWmWH9hC3GzHvG9kq0Rig_8dCU56eMsNUH8uUWHgD1Mz9jmwWH5XnoSJsL6smu9WHcBRARPd2UhQ6Klps1Zq5X9B6gxRdRqkiboPqpOMOgdfJS0b4JU3sLRhuI0z-3jYxzmrRF3sQckHr4rFd2LaIJsvI0wN8yaI7W6FWFyG9iPOEfZR1KasRMDhCrO_LGe3JMdLxZQbs1P2QeHCmebQfzaNMzYEHGF3ofArMKUGbG3WyrwGPpv31HSYtP3MmXXSl2NM2J0gkWB2CiZW-c5wwOt7jOM5dmB5GL93uqjmDWLcMMFEVIWxA140rt9aKi6raH6NE3x7EfI8FTT9WnFNkpyp0CF1mlnL2KX4e0q6wc14N6ayb6XNkzJD3RE4J4J3V53wJMvvsSw9ezmZvJOBrclvQoBljEPDRnX8VQMD1xGg6ghW8Y7fUrXyV_dz0tpI-gLJByfqIREAGBpDmUbYfsmcBT-cCTosleW_pq3bzfMjaLzBuytrnLoCktrregFeAsSRQRfO3jtsIZZ3W23n2eY-BXGbzX3MNg4rQITrolJ378fOwMUaA' https://github.onefiserv.net/_services/pipelines/_apis/distributedtask/packagedownload/agent/linux-x64/2.317.0
Invoke-WebRequest : Cannot bind parameter 'Headers'. Cannot convert the "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkZzekx
4enBtR1A5cVlCNUpfaVdaNVFwaERzdyJ9.eyJuYW1laWQiOiJkZGRkZGRkZC1kZGRkLWRkZGQtZGRkZC1kZGRkZGRkZGRkZGQiLCJzY3AiOiJBY3Rpb25zUnVudGltZS5QYWNrYWdlRG93bmxv 
YWQiLCJJZGVudGl0eVR5cGVDbGFpbSI6IlN5c3RlbTpTZXJ2aWNlSWRlbnRpdHkiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9zaWQiOi 
JERERERERERC1ERERELUREREQtRERERC1EREREREREREREREQiLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3ByaW1hcnlzaWQiOiJk 
ZGRkZGRkZC1kZGRkLWRkZGQtZGRkZC1kZGRkZGRkZGRkZGQiLCJhdWkiOiJlN2Y4NDIxYS00YjdkLTRiOTAtODQxNS0yOWY1ZDBlOTVhNzUiLCJzaWQiOiI2OTg4ZTZiZi0zYTEzLTRlNmMtOG 
Y1Yy1jYTc0YjA1Mjg4MTQiLCJpc3MiOiJnaXRodWIub25lZmlzZXJ2Lm5ldCIsImF1ZCI6ImdpdGh1Yi5vbmVmaXNlcnYubmV0L19zZXJ2aWNlcy92c3Rva2VuIiwibmJmIjoxNzUwMDQzMTMx 
LCJleHAiOjE3NTAwNDczMzF9.m3Ggo7UazQJ53Y_I6rPM6bpZOMcaxUs9aFY6pLx6CInUqS4iG9xGGEvUcPlsa4E06dSjXgmHrG-LzOSQ-hPVm9ZVDeDpYLd2onWJaxZFsDJGxFB7yAfmvlPWm 
WH9hC3GzHvG9kq0Rig_8dCU56eMsNUH8uUWHgD1Mz9jmwWH5XnoSJsL6smu9WHcBRARPd2UhQ6Klps1Zq5X9B6gxRdRqkiboPqpOMOgdfJS0b4JU3sLRhuI0z-3jYxzmrRF3sQckHr4rFd2LaI 
JsvI0wN8yaI7W6FWFyG9iPOEfZR1KasRMDhCrO_LGe3JMdLxZQbs1P2QeHCmebQfzaNMzYEHGF3ofArMKUGbG3WyrwGPpv31HSYtP3MmXXSl2NM2J0gkWB2CiZW-c5wwOt7jOM5dmB5GL93uqj 
mDWLcMMFEVIWxA140rt9aKi6raH6NE3x7EfI8FTT9WnFNkpyp0CF1mlnL2KX4e0q6wc14N6ayb6XNkzJD3RE4J4J3V53wJMvvsSw9ezmZvJOBrclvQoBljEPDRnX8VQMD1xGg6ghW8Y7fUrXyV 
_dz0tpI-gLJByfqIREAGBpDmUbYfsmcBT-cCTosleW_pq3bzfMjaLzBuytrnLoCktrregFeAsSRQRfO3jtsIZZ3W23n2eY-BXGbzX3MNg4rQITrolJ378fOwMUaA" value of type        
"System.String" to type "System.Collections.IDictionary".
At line:1 char:55
+ ... ar.gz -L -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (:) [Invoke-WebRequest], ParameterBindingException
    + FullyQualifiedErrorId : CannotConvertArgumentNoMessage,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
        
