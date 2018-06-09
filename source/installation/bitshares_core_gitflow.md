# BitShares Core - GitFlow

#### Contents:

- [Purpose](/source/installation/bitshares_core_gitflow.md#purpose)
- [Non-Consensus: Development / Release / Bugfix Workflows](/source/installation/bitshares_core_gitflow.md#non-consensus-development--release--bugfix-workflows)
- [Consensus: Development / Release / Bugfix Workflows](/source/installation/bitshares_core_gitflow.md#consensus-development--release--bugfix-workflows)
- [Goals To Achieve](/source/installation/bitshares_core_gitflow.md#goals-to-achieve)
- [Basic Rules](/source/installation/bitshares_core_gitflow.md#basic-rules)
- [How To Create a Release](/source/installation/bitshares_core_gitflow.md#how-to-create-a-release)
- [How To Create an Emergency Fix](/source/installation/bitshares_core_gitflow.md#how-to-create-an-emergency-fix)

***


## Purpose

The purpose of this document is to describe and define how changes flow into our code and through the various stages of development until it finally goes into production.

The general idea is based on [git-flow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

For our purposes, the general concept behind gitflow has been extended to allow for these additional needs:

1. We have two different types of releases, mainnet and testnet, with a master-like branch for each one.
2. We have to distinguish Consensus Impacting Changes (aka hardforks) from Non-Consensus Impacting Changes.

***

## Non-Consensus: Development / Release / Bugfix Workflows

<p align="center">
  <img src="/source/imgs/bts-non-concensus.png" width="800" title="BitShares Core GitFlow">
</p>

***

## Consensus: Development / Release / Bugfix Workflows

<p align="center">
  <img src="/source/imgs/bts-concensus.png" width="800" title="BitShares Core GitFlow">
</p>

***

## Goals To Achieve

1. Maintain two independent release versions, testnet and mainnet.
2. Decouple development from releases, i. e. maintain the ability to create emergency bugfixes for current release without bringing incomplete new features into production.
3. Separate consensus-related changes from non-consensus-related changes.
4. Keep development branches compatible with mainnet.

## Basic Rules

1. Development always happens in private feature-branches. The only exception is a change that must be distinguished in the destination branch (typical example: hardfork date in testnet).
2. Features are merged after they are reasonably complete, i. e. they come with unit tests that provide reasonable coverage and do not report any errors.
    1. "Completed" features that are not consensus-related are merged into "develop".
    2. "Completed" features that are consensus-related are merged into the "hardfork" branch, with a hardfork date in the far future.
    3. All merges into "develop" or "hardfork" are performed via github PR's and require review and approval from core source (if the PR is created by a core dev at least one other core dev must review and approve).
    4. To maintain a clean history and make reviewing and merging easier, feature branches must be rebased onto current "develop" (or "hardfork") before creating a PR.
    5. Merges are always done as real merges, not as fast-forwards, and not squashed.
3. Core devs coordinate regular merges from "develop" into "hardfork".
4. Both "develop" and "hardfork" should always remain compatible with mainnet, i. e. a full replay must be possible.

***

## How To Create a Release

For a release,

0. Check whether need to bump DB_VERSION to force a replay after upgraded: if there is a data schema change, or logic change that affects historical data, the answer is yes. FC version usually has been bumped already during development, but it doesn't harm if check again.
1. A "release" branch is created based on "develop" or "hardfork".
2. The "release" branch is merged into "testnet".
3. For a hardfork release, the hardfork date is adapted directly on the testnet branch.
4. The "testnet" branch is tagged as test-version.
5. Bugfixes for the release are created on the "release" branch and merged into "testnet". Additional test-versions are tagged as needed.
6. After sufficient testing, the release must be approved. In the case of a hardfork release, witness approval is required.
7. After approval, the mainnet hardfork date is decided and set in the "release" branch.
8. The "release" branch is merged into "master", and a version tag is created on "master".
9. The "release" branch is merged back into "develop" and "hardfork".
10. The "release" branch is merged into "testnet". This will produce a merge conflict for the hardfork dates, which must be resolved without changing the testnet hardfork date.
11. Update `Doxyfile` with the last version tag. Update online code documentation by using updated `Doxyfile` as config file in the master branch. Send pull request to https://github.com/bitshares/bitshares.github.io with new content in html format.
12. Update [download page of bitshares.org site](https://github.com/bitshares/bitshares.github.io/blob/master/_includes/download.html).

**Note:** Solving conflicts by github(web) will merge branches in unintended directions. Avoid solving this way, merge and resolve conflicts manually through the git command line. Conflicts generally occur when merging release to testnet.

**Note 2:** Follow command line github suggestion to resolve conflicts but at the end of the process you will not have permission to merge directly to `testnet`, never push the fix to `release`. Create a new branch and push there, then create a new pull request between `testnet` and `new_branch`, merge `new_branch` to `testnet` and `release` will be automatically added to the merge.

**Note 3:** When creating tag for testnet do it from the command line with `git tag`. Github don't have the option to create a tag without a release.

**Note 4:** ~~the tag commit can be changed.~~ Don't change tags on github. This is a source of confusion, and of irreproducible bug reports. Make new one is better (ex: test-2.0.180321b or wait 1 day).

**Note 5:** Do not mark releases as "pre release" unless there is a real new version coming immediately after. Never upgrade "pre release" to "release" as new emails to subscribers will not be sent when doing so.

***

## How To Create an Emergency Fix

An emergency fix may become necessary when a serious problem in mainnet is discovered. The goal here is to fix the problem as soon as possible, while keeping the risk for creating additional problems as low as possible.

First of all, the problem must be analyzed and debugged. This happens, naturally, directly on the release version.

Presumably the developer who creates the fix will work on his private master branch. That is OK. But for publishing the fix, the following steps should be taken:

<p align="center">
  <img src="/source/imgs/bts-emergency-fix.png" width="800" title="BitShares Core GitFlow">
</p>

1. The fix is applied to the version of the "release" branch that was merged into "master" when creating the broken release version.
2. The "release" branch is merged into "master", and a version tag is created on "master".
3. Witnesses update to the new version, and production continues.
4. A unit test is created on "develop" that reproduces the problem.
5. The "release" branch is merged into "develop", and it is verified that the fix resolves the problem, by running the unit test.
6. The "release" branch is merged into "hardfork" and "testnet".


***

(ref)

- https://github.com/bitshares/bitshares-core/wiki/Git-Flow


