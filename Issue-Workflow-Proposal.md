# Specify 7 Issue Workflow Proposal
This proposal puts forward the possibility of using the GitHub issue tracker for handling Specify 7 bugs.

## Issue Creation
[New issues](https://github.com/specify/specify7/issues/new) should be entered into the GitHub tracker through the GitHub interface and assigned a *type* and a *priority* using GitHub issue labels.

### Type Labels
* **type:bug** - The issue describes incorrect behavior of the product.
* **type:enhance** - The issue describes improvements or extensions to existing behavior.
* **type:design** - The issue describes behavior that requires a design decision to be resolved.

### Priority Labels
* **pri:block** - The issue must be resolved before releasing even if entered after a freeze.
* **pri:high** - The issue must be resolved before releasing.
* **pri:normal** - The issue should be resolved before releasing but may be deferred to the next release.
* **pri:low** - The issue should only be resolved in the current release if all higher priority issues are resolved.

This set of labels can be extended on as needed basis.

## Milestones
GitHub [milestones](https://github.com/specify/specify7/milestones) should be used to organize issues resolutions into releases. Issues assigned to **prerelease** are those to be considered for the closest upcoming release. Issues which are to be deferred to the following release should be assigned to **next-release**. Other issues should not be assigned to any milestone to indicate unspecified future release.

Milestones will be managed across releases as follows. Any unresolved issues in **prerelease** will be reassigned to **next-release**. The **prerelease** milestone will be renamed with the release version and closed. The **next-release** milestone will be renamed as the new **prerelease**, and a new **next-release** milestone will be created.

## Issue Resolution Workflow
[Open issues](https://github.com/specify/specify7/issues?q=is%3Aopen+is%3Aissue+milestone%3Aprerelease) in **prerelease** should be addressed by a team member according to priority. Once a solution to the issue has been committed, the issue will be *closed*.

[Unresolved closed issues](https://github.com/specify/specify7/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3Aprerelease+is%3Aclosed+-label%3Aresolved) should be verified as being resolved by QA staff and either labeled **resolved** or *re-opened*.

## Reporting
In the interest of continuity, a live Bugzilla style [report](https://rawgit.com/benanhalt/3224d141f93c6e1da05c/raw/specify7-issues.html) can be generated from a [script](https://gist.github.com/benanhalt/3224d141f93c6e1da05c) using the GitHub API. The report is organized according to the release schedule and sorted by resolution, status and priority. 
