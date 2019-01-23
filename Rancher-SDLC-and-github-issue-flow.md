The following describes the typical flow of an issue through our SDLC and the labels that should be applied at various stages

1. When a dev starts working on issue, even if it is at the design phase, add the `status/working` label.
1. If the issue is going to require a UI change, open a corresponding UI ticket. Add the `area/ui` label to it and assign to the UI dev that you know will be working on it or `@westlywright` if you don't know. Link to the main issue. Also, describe the UI change that is needed. Add the `status/awaiting-backend` label so that the UI team knows it is not yet ready.
1. When your code is ready for review, you must first have someone peer review it. Add the `status/ready-for-review` label and reach out to peers to review your PRs.
1. Once you have an LGTM from a peer on all PRs related to the issue, add the `status/resolved` label. This indicates that it is ready for review from a tech lead and ready to merge after that.
1. PRs will now be reviewed by a lead. If changes are needed, the `status/resolved` label will be removed.
1. Once all PRs for an issue have been merged and a build is available with the change, assign the issue to QA for testing by adding the label `status/to-test` and assigning to either the QA that opened the issue or the test lead. The dev that made the changes should remain assigned to the issue, in addition to a QA.
1. If the issue also has a UI change, remove the `status/awaiting-backend` label from the UI issue and let someone from the UI team know it is ready to be worked on.
1. If an issue needs to go back for fixes from QA, the QA should remove the `status/resolved` and `status/to-test` labels, add the `status/reopened` label, and ensure the dev is still assigned
1. Once the issue passes all testing, the QA can close it