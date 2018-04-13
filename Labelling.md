---
title: Labeling
---

This page is to explain the conventions over Parity's labeling system.

Labels are split into several groups.

- 'A' group is used for code-review status and are applicable only to Pull Requests.
- 'B' group is used for pull requests for upcoming beta releases.
- 'F' group is used to encode the type (and accordingly the severity) of issues; they are applicable only within the Issue Tracker.
- 'M' group is to encode the affected component or sub-project.
- 'P' group is to denote priority. They are generally relevant only to issues, though may in principle be used on pull-requests.
- 'Q' group is to denote difficulty.
- 'Z' group are reasons for why something is a non-issue. They are applicable only within the Issue Tracker.

As such, a pull request must have a single label from the 'A' group and may additionally have a single label from the 'P' group (though typically will not).

An valid issue must have a single label from the 'F' group and may additionally have a single label from the 'P' group. An invalid issue should be closed with a single label from the 'Z' group.

### 'A' group

All pull requests should start labeled with either 'A0-pleasereview', 'A3-inprogress', 'A8-backport' (in the base of an already-reviewed back-port), or 'A2-insubstantial' (should it be an alteration which requires no code review). After review it should be relabeled with another 'A' group label.

- ![A0-pleasereview](http://i.imgur.com/8lr1M4r.png) Pull request needs code review.
- ![A1-onice](http://i.imgur.com/xZ9YJvB.png) Pull request is reviewed well, but should not yet be merged.
- ![A2-insubstantial](http://i.imgur.com/xPahkpz.png) Pull request requires no code review (e.g. a sub-repository hash update).
- ![A3-inprogress](http://i.imgur.com/rQbszDc.png) Pull request is in progress. No review needed at this stage.
- `A3-stale` Pull request did not receive any updates in a long time. No review needed at this stage and should be closed if author does not indicate any further work is done.
- `A4-awaitingci` Pull request is waiting for changes on the CI to complete tests before review/merge can begin.
- ![A4-clasignoffneeded](http://i.imgur.com/QSNNtk0.png) Pull request requires author to sign off on CLA before review/merge can begin.
- ![A4-gotissues](http://i.imgur.com/fUyJ8PO.png) Pull request is reviewed and has significant issues which must be addressed. Once addressed, author should relabel as `A0-pleasereview`.
- ![A5-grumble](http://i.imgur.com/fD1vO9N.png) Pull request has minor issues that must be addressed before merging. This may require only replying to comments. Pull request should be relabeled as `A0-pleasereview` to get a final sign-off from a reviewer.
- ![A6-mustntgrumble](http://i.imgur.com/hGDrT93.png) Pull request has areas for improvement. The author need not address them before merging.
- ![A6-revert](http://i.imgur.com/0EILx22.png) Pull request that reverts recent changes.
- ![A7-looksgoodcantmerge](http://i.imgur.com/3yYImuZ.png) Pull request is reviewed well, but cannot be merged due to conflicts.
- ![A7-looksgoodtestsfail](http://i.imgur.com/4APg6fE.png) Pull request is reviewed well, but cannot be merged due to tests failing.
- ![A8-backport](http://i.imgur.com/VnFVyWk.png) Pull request is already reviewed well in another branch.
- ![A8-looksgood](http://i.imgur.com/XfRlSzA.png) Pull request is reviewed well.
- ![A9-buythatmanabeer](http://i.imgur.com/MycyedU.png) Pull request is reviewed well and worth buying the author a beer.
- ![A9-FUCKYEAH!](http://i.imgur.com/HZQeL2M.png) Pull request is reviewed well and can not be compensated with any amount of beer in the galaxy ;)

### 'B' group

Used on pull requests only. Denotes tasks for the next beta release.

- ![B0-patch](http://i.imgur.com/WVij4kk.png) Pull request should also be back-ported to the beta branch.
- ![B1-relnotes](http://i.imgur.com/eFiTrqa.png) Changes should be mentioned in the release notes of the next major version.

### 'F' group

Issues should have only one of these. Do not combine; if multiple labels are equally applicable to an issue, use the one with the lowest number.

- ![F0-consensus](http://i.imgur.com/IiVrLJn.png)  Issue can lead to a consensus failure.
- ![F1-panic](http://i.imgur.com/PvDKeCe.png) The client panics and exits without proper error handling.
- ![F1-security](http://i.imgur.com/0PrqmGX.png) The client fails to follow expected, security-sensitive, behaviour.
- ![F2-bug](http://i.imgur.com/EJhD1MQ.png) The client fails to follow expected behavior.
- ![F3-annoyance](http://i.imgur.com/Zsd8zxB.png) The client behaves within expectations, however this "expected behaviour" itself is at issue. Annoyances are small enhancements which dramatically improve the usability of the client.
- ![F4-tests](http://i.imgur.com/jFiH84M.png) Tests need fixing, improving or augmenting.
- ![F5-documentation](http://i.imgur.com/iYgnzcd.png) Documentation needs fixing, improving or augmenting.
- ![F6-refactor](http://i.imgur.com/HCloAwV.png) Code needs refactoring.
- ![F7-footprint](http://i.imgur.com/lE1ESzo.png) An enhancement to provide a smaller (system load, memory, network or disk) footprint.
- ![F7-optimisation](http://i.imgur.com/DNym8en.png) An enhancement to provide better overall performance in terms of time-to-completion for a task.
- ![F8-enhancement](http://i.imgur.com/ziRfEiW.png) An additional feature.
- `F9-meta` A specific issue for grouping tasks or bugs of a specific category.
- ![F9-release](http://i.imgur.com/zl4BiqW.png) A specific release. All such issues should be templated on [1387](https://github.com/paritytech/parity/issues/1387).

### 'M' group

Used to denote the affected component or sub-project. Each issue and pull request should have (at least) one.

- ![M0-build](http://i.imgur.com/vGzi4al.png) Building and build system
- ![M1-ci](http://i.imgur.com/KzZMc2p.png) Continuous integration
- `M2-config` Chain specifications and node configurations
- ![M2-installer](http://i.imgur.com/9nEQ4RW.png) Installers for MacOS and Windows
- ![M4-core](http://i.imgur.com/Pf9himB.png) Core client
- ![M5-binaries](http://i.imgur.com/W5ECLIM.png) External binaries (ethkey, ethstore, ethvm, etc.)
- ![M6-rpcapi](http://i.imgur.com/kZGX67C.png) RPC API
- ![M7-signer](http://i.imgur.com/FlogNRD.png) Trusted signer
- ![M7-ui](http://i.imgur.com/bTvS99W.png) User Interface and wallet
- `M8-contracts` Solidity smart contracts
- ![M8-dapp](http://i.imgur.com/LgIN8xw.png) Decentralized applications
- ![M9-deploy](http://i.imgur.com/C2GsjWb.png) Deployment and registration

### 'P' group

Typically used only to annotate issues, however P0 and P2 may reasonably be used on PRs in exceptional circumstances.

- ![P0-dropeverything](http://i.imgur.com/GW63MgN.png) Everyone should address the issue/PR now.
- ![P2-asap](http://i.imgur.com/Lqi1wHj.png) No need to stop dead in your tracks, however issue/PR should be addressed as soon as possible.
- ![P5-sometimesoon](http://i.imgur.com/HI0gW9B.png) Issue is worth doing soon.
- ![P7-nicetohave](http://i.imgur.com/WlcjjSv.png) Issue is worth doing eventually.
- ![P9-somedaymaybe](http://i.imgur.com/r9qQFId.png) Issue might be worth doing eventually.

### 'Q' group

Is used to annotate difficulty of issues.

- ![Q0-trivial](http://i.imgur.com/K8BWrSm.png) Can be fixed by anyone with access to a computer.
- `Q1-mentor` An easy task were a mentor is available. Please indicate in the issue who the mentor could be.
- ![Q2-easy](http://i.imgur.com/CTJ9eVc.png) Can be fixed by copy and pasting from StackOverflow.
- ![Q5-substantial](http://i.imgur.com/HdotN9f.png) Can be fixed by a developer with decent experience.
- ![Q7-involved](http://i.imgur.com/OSQ0tc6.png) Can be fixed by a team of developers and probably takes some time.
- ![Q9-epic](http://i.imgur.com/Q3mBYJy.png) Can only be fixed by John Skeet.

### 'Z' group

Used only on issues which will (or _may_, in the case of Z5) be closed immediately.

- ![Z0-duplicate](http://i.imgur.com/SBxVhGd.png) Issue is a duplicate. Closer should comment with a link to the duplicate.
- ![Z0-intended](http://i.imgur.com/qbfIlvR.png) Issue describes a behavior which turns out to work as intended. Closer should explain why.
- ![Z0-invalid](http://i.imgur.com/dOX7QyV.png) Issue is invalid. Closer should comment why.
- ![Z0-question](http://i.imgur.com/EN3xLJn.png) Issue is a question. Closer should answer.
- ![Z0-stale](http://i.imgur.com/Ds2ZUIl.png) Issue is in principle valid, but it is not relevant anymore or can not reproduced.
- ![Z0-wontfix](http://i.imgur.com/pPJTV2h.png) Issue is in principle valid, but this project will not address it. Closer should explain why.
- ![Z5-unconfirmed](http://i.imgur.com/X747d5L.png) Issue might be valid, but it's not yet known.

### Issue-Checklist

0. Have you tried turning it off and on again?
1. Are you fully synchronized?
2. Which version of Parity are you running? ("master" or "git" is not a version)
3. Which operating system are you running?
4. How did you install Parity? (binary, installer, from source, which branch?)
5. What flags are you running Parity with?
6. Do you use any config file?
7. Please, share the exact error message!
8. Is there anything in the logs?
9. Are you connected to any peers?
0. What's your time? Is it synchronized? [time.is](https://time.is)
1. Did you answer all my questions yet?
