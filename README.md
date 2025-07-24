All service repos must be children of https://gitlab.onefiserv.net/issuers/dispute/disputesworkspace
Naming convention: is-dws-[fraud/disputes]-[domain]. Do not use Subgroups.
Protect master and Release branches. Use release branch only if absolutely needed.
2 approvals required from any developer.
Tech leads must be default reviewers.
1 approval required from default reviewers.
Delete branches automatically on merge.
Only tech leads can merge.
Commits and MR must refer to the associated Jira ticket.
Integration with NSA Jenkins. Follow instructions here: https://enterprise-confluence.onefiserv.net/display/AE/CI-CD+Pipeline+Adoption. Use gfsCloudPipelineV2 for Java projects and gfsNodeJsUIPipeline for JavaScript projects.
Integration with Jira. Use https://enterprise-jira.onefiserv.net/rest/bitbucket/1.0/webhook/gitlab (Push and Merge Requests)
APM label is required.
Listed here: Disputes Workspace UAID-10256
