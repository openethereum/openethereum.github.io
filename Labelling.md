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

Issues should have only one of these. Do not combine; if multiple labels are equally applicable to an issue, use the one with the lowest number.

- F0-consensus: Issue can lead to a consensus failure.
- F1-security: A security flaw: This can lead to failure of some security-sensitive feature.
- F2-bug: A bug. The client fails to follow expected behaviour.
- F2-build: A bug with the build. Client fails to build on a supported platform.
- F3-annoyance: Client behaves within expectations, however this behaviour is nonetheless really annoying.
- F4-tests: Tests need fixing, improving or augmenting.
- F5-documentation: Documentation needs fixing, improving or augmenting.
- F6-refactor: Code needs refactoring.
- F7-footprint: An enhancement to provide a smaller (system load, memory, network or disk) footprint.
- F7-optimisation: An enhancement to provide better overall performance in terms of time-to-completion of a task.
- F8-enhancement: An additional feature.
- F9-release: A specific release. All such issues should be templated on [1387](https://github.com/ethcore/parity/issues/1387).

### 'P' group

Typically used only to annotate issues, however P0 and P2 may reasonably be used on PRs in exceptional circumstances.

- P0-dropeverything: Everyone should address the issue/PR now.
- P2-asap: No need to stop dead in your tracks, however issue/PR should be addressed as soon as possible.
- P5-sometimesoon: Issue is worth doing soon.
- P7-nicetohave: Issue is worth doing eventually.
- P9-somedaymaybe: Issue might be worth doing eventually.


### 'Z' group

Used only on issues which will (or may, in the case of Z5) be closed immediately.

- Z0-duplicate: Issue is a duplicate. Closer should comment with a link to the duplicate.
- Z0-invalid: Issue is invalid. Closer should comment why.
- Z0-question: Issue is a question. Closer should answer.
- Z0-wontfix: Issue is in principle valid, but this project will not address it. Closer should explain why.
- Z5-unconfirmed: Issue might be valid, but it's not yet known.
