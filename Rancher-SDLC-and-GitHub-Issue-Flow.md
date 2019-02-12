## Issue flow

The following describes the typical flow of an issue through our SDLC and the labels that should be applied at various stages

### 1. Dev begins working on issue
Add the `status/working` label, even if you are just in the design phase

Identify if the issue requires a **UI change** and open a UI ticket accordingly.
* Label it with `area/ui` `kind/feature-component` and `status/awaiting-backend`
* Assign to UI lead, or a specific UI dev if you know it must go to themif you don't know.
* Link to the main issue.
* Describe the UI change that is needed and the API change that enables it. ***Dont simply say "UI for issue X".***

### 2. Code is ready for review
When your code is ready for review, you must first have someone **peer review** it. Add the `status/ready-for-review` label and reach out to peers to review your PRs.

Once you have an LGTM from a peer on all PRs related to the issue, add the `status/resolved` label. This indicates that it is **ready for final review and merge.**

### 3. Code gets merged!
Once all PRs for an issue have been merged and a build is available with the change, assign the issue to QA for testing by adding the label `status/to-test` and assigning to either the QA that opened the issue or the test lead. The dev that made the changes should remain assigned to the issue, in addition to a QA. Often, the tech lead that merged the issue will do this step.

If the issue also has a UI change, remove the `status/awaiting-backend` label from the UI issue and let someone from the UI team know it is ready to be worked on.

### 4. Testing
If an issue needs to go back for fixes from QA, the QA should remove the `status/resolved` and `status/to-test` labels, add the `status/reopened` label, and ensure the dev is still assigned.

QA may, at their discretion, open up new issues to track smaller bugs related to the issue

Once the issue passes all testing, the QA can close it

### Process Notes
You do not need to remove status labels as an issue moves foward in the process. So, when you transition from working to ready-for review, you don't need to remove the `status/working` label. Just add `status/ready-for-review`.

When issues go backwards (ie, back to working from review or testing), status labels should be removed to reflect where the issue is in the process.

Some projects require us to make a release before an issue can be tested. These include: cli, helm/tiller (our fork), and RKE. Coordinate with a tech lead accordingly.

We currently track issues in three places:
* rancher/rancher
* rancher/rke
* our internal security issue tracker

Please be aware of all three!

## Issue categorization
To track the type of work we have in the pipeline, we categorize issues using `kind/*` labels. Here are the main "kinds" you should be working with:
* `kind/feature` - Issues that represent larger new pieces of functionality, not enhancements to existing functionality
* `kind/feature-component` - For issues that represent a subset of a larger feature that is being developed
* `kind/enhancement` - Issues that improve or augment existing functionality
* `kind/performance` - Issues related directly to performance improvements
* `kind/bug` - Issues that are defects reported by users or that we know have reached a real release
* `kind/bug-qa` - Issues that have not yet hit a real release. In other words, bugs introduced by a new feature or enhancement that have not yet hit a real release.
* `kind/task` - Issues that represent work that needs to be done, but don't involve a code change
* `kind/question` - Issues that just require an answer. No code change needd