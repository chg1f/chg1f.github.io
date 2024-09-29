---
title: "Git Convention"
date: "2022-07-19"
tags: ["note", "git", "convention"]
---

## Convention

> https://git-scm.com/book/en/v3

### Branch

> https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

> https://github.com/nvie/gitflow

> https://nvie.com/posts/a-successful-git-branching-model/

|type|description|
|:-:|:-|
|feature/<FEATURE_SUMMARY>|Feature branches are created from develop, When a feature is complete it is merged into the develop branch|
|bugfix/<BUG_SUMMARY>(or hotfix/<BUG_SUMMARY>)|If an issue in main is detected a hotfix branch is created from main|
|release/<VERSION>|A release branch is created from develop, When the release branch is done it is merged into develop and main|
|develop|A develop branch is created from main|
|master(or main)|The main branch stores the official release history, and the develop branch serves as an integration branch for features. It's also convenient to tag all commits in the main branch with a version number.|

### Commit

> https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format

> https://github.com/commitizen/conventional-commit-types/blob/master/index.json

> https://www.conventionalcommits.org/en/v1.0.0/

```text
<type>(<scope>)[!]: <short summary>
  │       │     │        │
  │       │     │        └─⫸ Summary in present tense. Not capitalized. No period at the end.
  │       │     │
  │       │     └─⫸ If ! is used, BREAKING CHANGE: MAY be omitted from the footer section, and
  │       │         the commit description SHALL be used to describe the breaking change.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|docs-infra|migrations|ngcc|ve|
  │                          devtools
  │
  └─⫸ Commit Type
```

|type|description|
|:-:|:-|
|feat|A new feature|
|fix|A bug fix|
|test|Adding missing tests or correcting existing tests|
|ci|Changes to our CI configuration files and scripts (examples:CircleCi, SauceLabs)|
|build|Changes that affect the build system or external dependencies (example scopes:gulp, broccoli, npm)|
|docs|Documentation only changes|
|perf|A code change that improves performance|
|refactor|A code change that neither fixes a bug nor adds a feature|
|style|Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)|
|chore|Other changes that don't modify src or test files (version.txt and release changes)|
|misc|Miscellaneous types|
|wip|Working in process|
