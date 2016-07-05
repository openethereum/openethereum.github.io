This page is to explain the conventions over Parity's labelling system.

Labels are split into 4 groups.

- 'A' group is used for code-review status and are applicable only to Pull Requests.
- 'F' group is used to encode the type (and accordingly the severity) of issues; they are applicable only within the Issue Tracker.
- 'P' group is to denote priority. They are generally relevant only to issues, though may in principle be used on pull-requests.
- 'Z' group are reasons for why something is a non-issue. They are applicable only within the Issue Tracker.

As such, a pull request must have a single label from the 'A' group and may additionally have a single label from the 'P' group (though typically will not). 

An valid issue must have a single label from the 'F' group and may additionally have a single label from the 'P' group. An invalid issue should be closed with a single label from the 'Z' group.

### 'A' group

All pull requests should start labelled with either 'A0-pleasereview', 'A1-inprogress', 'A7-backport' (in the base of an already-reviewed back-port), or 'A9-insubstantial' (should it be an alteration which requires no code review). After review it should be relabelled with another 'A' group label.

- ![A0-pleasereview](http://i.imgur.com/aTHP7W6.png) Pull request needs code review.
- ![A1-inprogress](http://i.imgur.com/CBobkPF.png) Pull request is in progress. No review needed at this stage.
- ![A2-clasignoffneeded](http://i.imgur.com/AXca6S5.png) Pull request requires author to sign off on CLA before review/merge can begin.
- ![A2-gotissues](http://i.imgur.com/gWuhzvN.png) Pull request is reviewed and has significant issues which must be addressed. Once addressed, author should relabel as A0-pleasereview.
- ![A3-justtoobig](http://i.imgur.com/g3X5EaJ.png) Pull request is too large. It should be broken down into smaller PRs; when reduced in size PR may be relabelled as A0-pleasereview.
- ![A4-grumble](http://i.imgur.com/MeIiPJM.png) Pull request has minor issues that must be addressed before merging. This may require only replying to comments. Pull request should be relabelled as A0-pleasereview to get a final sign-off from a reviewer.
- ![A5-mustntgrumble](http://i.imgur.com/5WeFsnC.png) Pull request has areas for improvement. The author need not address them before merging.
- ![A6-looksgoodcantmerge](http://i.imgur.com/BQXeTN1.png) Pull request is reviewed well, but cannot be merged due to conflicts.
- ![A7-backport](http://i.imgur.com/Otz1UPg.png) Pull request is already reviewed well in another branch.
- ![A7-looksgood](http://i.imgur.com/JfSyfDU.png) Pull request is reviewed well.
- ![A8-onice](http://i.imgur.com/dBBMF6D.png) Pull request is reviewed well, but should not yet be merged.
- ![A9-insubstantial](http://i.imgur.com/FOELWqp.png) Pull request requires no code review (e.g. a sub-repository hash update).

### 'F' group

Issues should have only one of these. Do not combine; if multiple labels are equally applicable to an issue, use the one with the lowest number.

- ![F0-consensus](http://i.imgur.com/IhjbDHF.png) Issue can lead to a consensus failure.
- ![F1-security](http://i.imgur.com/sMKDlSk.png) The client fails to follow expected, security-sensitive, behaviour.
- ![F2-bug](http://i.imgur.com/eYr94de.png) The client fails to follow expected behaviour.
- ![F2-build](http://i.imgur.com/LYcizm5.png) The client fails to build on a supported platform.
- ![F3-annoyance](http://i.imgur.com/1PdxguG.png) The client behaves within expectations, however this "expected behaviour" itself is at issue. Annoyances are small enhancements which dramatically improve the usability of the client.
- ![F4-tests](http://i.imgur.com/ELwMjJY.png) Tests need fixing, improving or augmenting.
- ![F5-documentation](http://i.imgur.com/Pwoxrzr.png) Documentation needs fixing, improving or augmenting.
- ![F6-refactor](http://i.imgur.com/f0NlH2r.png) Code needs refactoring.
- ![F7-footprint](http://i.imgur.com/2h7yXRV.png) An enhancement to provide a smaller (system load, memory, network or disk) footprint.
- ![F7-optimisation](http://i.imgur.com/gt1qrE0.png): An enhancement to provide better overall performance in terms of time-to-completion for a task.
- ![F8-enhancement](http://i.imgur.com/HAbBBkZ.png) An additional feature.
- ![F9-release](http://i.imgur.com/AnsQsha.png) A specific release. All such issues should be templated on [1387](https://github.com/ethcore/parity/issues/1387).

### 'P' group

Typically used only to annotate issues, however P0 and P2 may reasonably be used on PRs in exceptional circumstances.

- ![P0-dropeverything](http://i.imgur.com/9xgwh8X.png) Everyone should address the issue/PR now.
- ![P2-asap](http://i.imgur.com/9xYlkh1.png) No need to stop dead in your tracks, however issue/PR should be addressed as soon as possible.
- ![P5-sometimesoon](http://i.imgur.com/kuqWYX5.png) Issue is worth doing soon.
- ![P7-nicetohave](http://i.imgur.com/Bcf2wnY.png) Issue is worth doing eventually.
- ![P9-somedaymaybe](http://i.imgur.com/5JWp6Uz.png) Issue might be worth doing eventually.

### 'Z' group

Used only on issues which will (or may, in the case of Z5) be closed immediately.

- ![Z0-duplicate](http://i.imgur.com/nOW8MzJ.png) Issue is a duplicate. Closer should comment with a link to the duplicate.
- ![Z0-invalid](http://i.imgur.com/MYi7xoc.png) Issue is invalid. Closer should comment why.
- ![Z0-question](http://i.imgur.com/osUCE6S.png) Issue is a question. Closer should answer.
- ![Z0-wontfix](http://i.imgur.com/aKW3aRq.png) Issue is in principle valid, but this project will not address it. Closer should explain why.
- ![Z5-unconfirmed](http://i.imgur.com/I6LT7Mv.png) Issue might be valid, but it's not yet known.