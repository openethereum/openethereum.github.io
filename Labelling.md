This page is to explain the conventions over Parity's labelling system.

Labels are split into 4 groups.

- 'A' group is used for code-review status and are applicable only to Pull Requests.
- 'F' group is used to encode the type (and accordingly severity) of issues; they are applicable only within the Issue Tracker.
- 'P' group is to denote priority. They are generally relevant only to issues, though may in principle be used on pull-requests.
- 'Z' group are reasons for why something is a non-issue. They are applicable only within the Issue Tracker.

As such, a pull request must have a single label from the 'A' group and may additionally have a single label from the 'P' group (though typically will not). 

An valid issue must have a single label from the 'F' group and may additionally have a single label from the 'P' group. An invalid issue should be closed with a single label from the 'Z' group.

### 'A' group

All pull requests should start labelled with either 'A0-pleasereview', 'A1-inprogress', 'A7-backport' (in the base of an already-reviewed back-port), or 'A9-insubstantial' (should it be an alteration which requires no code review). After review it should be relabelled with another 'A' group label.

- A0-pleasereview: Pull request needs code review.
- A1-inprogress: Pull request is in progress. No review needed at this stage.
- A2-clasignoffneeded: Pull request requires author to sign off on CLA before review/merge can begin.
- A2-gotissues: Pull request is reviewed and have significant issues.
- A3-justtoobig: Pull request is too large. It should be broken down into smaller PRs.
- A4-grumble: Pull request has minor issues that must be addressed before merging. This may require only replying to comments.
- A5-mustntgrumble: Pull request has areas for improvement. The author need not be address them before merging.
- A6-looksgoodcantmerge: Pull request is reviewed well, but cannot be merged due to conflicts.
- A7-backport: Pull request is already reviewed well in another branch.
- A7-looksgood: Pull request is reviewed well.
- A8-onice: Pull request is reviewed well, but should not yet be merged.
- A9-insubstantial: Pull request requires no code review (e.g. a subrepository hash update).

### 'F' group
- F0-consensus
- F1-security
- F2-bug
- F2-build
- F3-annoyance
- F4-tests
- F5-documentation
- F6-refactor
- F7-footprint
- F7-optimisation
- F8-enhancement
- F9-release


- P0-dropeverything
- P2-asap
- P5-sometimesoon
- P7-nicetohave
- P9-somedaymaybe


- Z0-duplicate
- Z0-invalid
- Z0-question
- Z0-wontfix
- Z5-unconfirmed
