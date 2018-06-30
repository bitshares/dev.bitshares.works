# Contribution Guide

## [DRAFT] BitShares Contribution Guidance Using GitHub

This document, as a draft, intends to provide guidance and solicit feedback from Team Members and the Community. It does not intend to be a directive at this time. The goal is to explore, refine and finally define a collaboration framework using GitHub. Provided below are links to sample items for demonstration purposes. After reading the article, you are encouraged to apply the concepts introduced within the [Contribution Guide Project](https://github.com/bitshares/bitshares-core/projects/6) for further refinement. Please provide feedback by opening an Issue or commenting therein. Once the Core Team has refined their processes, this document will loose its [DRAFT] status and we apply the concepts throughout the BitShares-Core repo.

## TABLE OF CONTENTS

## INTRODUCTION

Collaboration within open source projects is key to advancing their development and adoption. GitHub serves as the central repository for code and collaboration today and the intention is to continue using this transparent platform. GitHub provides a flexible platform upon which can craft our framework. Software developers are likely familiar with the concepts of Issues and Comments as provided by GitHub, but perhaps less so in extending them to represent User Stories or Tasks. Labels, Projects and Milestones are other concepts provided by GitHub which allow categorization, organization and prioritization both visually and functionally. These concepts are introduced below and demonstrated to apply them when contributing to the BitShares-Core repository.

## CONCEPTS

For the benefit of a reader unfamiliar with the GitHub platform, the concepts below are offered in contextual ordering rather than alphabetically. The concepts intend to build upon each other as the reader progresses.

- Agile software development

Agile software development is is an umbrella term for a set of methods and practices based on the values and principles expressed in the [Agile Manifesto](http://agilemanifesto.org/). Solutions evolve through collaboration between self-organizing, cross-functional teams utilizing the appropriate practices for their context.

Please note: The BitShares Core Team intends to adopt agile principals where appropriate, but not be strictly held to them.

- GitHub Repository

GitHub is a software platform for teams to organize their efforts. This is the highest level concept discussed herein. The reader finds themselves within the BitShares-Core repository. The concepts offered and their application are intended to be limited to this repository only.

- Issues

An `Issue` as implemented within the GitHub platform is quite broad, leaving open to the development team how to use them for collaboration. At its core, GitHub is an issue tracker - a collection of tasks, enhancements, bugs and more. `Issues` are the primary method for communicating ideas within the platform. This is enabled by using various properties and features of Issues including: `Title`, `Description`, `Comments`, `Assignees`, `Labels`, `Projects` and `Milestones` (read on to learn about these).

- Tasks

A `Task` is a semantic application of an `Issue` to represent work to be performed within the repository. Typically a Task will be assigned to a `Team Member`. A `Task` may be the highest level or a child of `Task List`, `User Story` or `Epic` (see below).

- Task List

A `Task List` is a collection of `Issues` representing a progression of work to be done. A `Task List` must be represented within the `Description` of an `Issue` using the "- [ ]" pattern (dash-space-bracket-space-bracket) to represent an open/unfinished `Task` and the "- [x]" pattern (dash-space-bracket-x-bracket) to represent completed. They can be `inline` or `referential`. An `inline` `Task List` will contain its elements within the `Task`, `User Story` or `Epic`. A `referential` `Task List` will contain references to other `Tasks` using the hash tag "#" followed by the GitHub IssueID assigned when the `Issue` was created. Note: the `Issue` being referenced must exist, so when creating a `referential` `Task List` one my need to return to update after creating the target `Task`.

- User Stories

A `User Story` is an `agile software development` concept used to represent a requirement of the system.

- Labels
- Projects
- Milestones
- Wiki


## Contributing and Applying the Above Concepts

***

#### Contributor
@ryanRfox



