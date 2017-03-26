# How do we develop software

## Workflow

![alt tag](git-flow.png)

* We use `master` branch as a main development branch.  In some
  traditional workflows this branch is called `develop`.  This branch
  is semi-stable.  It means that this branch must compile, tests must
  pass, it shouldn't have stupid errors like you run cardano-sl nodes
  and see `notImplemented` errors.  If something like this happens,
  someone must fix it ASAP.  At the same time, it is legal to have
  some bugs in this branch which are really hard to fix. Of course we
  should try to avoid it, but if someone pushed something to this
  branch and then it turned out there is a bug which is hard to fix,
  it doesn't mean that someone must fix it ASAP. So please don't
  expect `master` to always work.
* We also have `stable` branch which contains latest released version
  which has been thoroughly tested. Even though we test thoroughly
  before release, sometimes there can be bugs even in `stable`
  branch because of complexity of our software. Such bugs must be
  reported and addressed ASAP.
* Only allowed team-members can push directly to `master`, `stable`
  and release branches.
* Features, fixes, refactoring must be done in separate branches.
  * They are reviewed and merged after that.
* New tagged release (even minor one) is a new branch.
* If there is a release then a `tag` is created from the commit of this release
  branch.
* We use release branches for GitHub Releases page, please see [one](https://github.com/input-output-hk/cardano-sl/releases) for Cardano SL project.
* Fixes from release branches are merged into `master`.

## Branches

* Branch name should fit the following template: `(type of task)/(maybe related
  issue number)(name)`.
  * `bugfix/dae31-fix-tx-send`
  * `feature/csl492-wallet-delegation-certs`
  * `feature/launcher`

## Commits

We follow [this guide](http://chris.beams.io/posts/git-commit/) how to write a
commit message. Also if commit is related to an issue then we add the issue name
at the beggining (i.e. `[CSL-659] Introduce Pos.Communication.Protocol`).

## Pull requests

When you finish your work in your branch, then you should create a pull request
to your project. Create a PR to `master` branch (or any other branch if you were
explicitly asked). Please assign a reviewer (if you don't know a person then
project manager would do it himself). Do not merge your own PRs!

Please describe in PR what have you done, if it is tested or not, any additional
information.

## Merging Pull requests

After review by another team member, the teammate merging the PR should merge
`master` into the pull request's branch and then the PR branch into `master`,
with the `--no-ff` option enabled.

Generally, the pull request's branch should be merged into master , but if is
clear that rebasing this branch onto master is better (for instance, it is a
trivial change which doesn't deserve branching) then a rebase can be done
instead.

## Versions

Versions are denoted with 3 numbers: `Maj.Min.Fix`.

* `Maj` is major version which changes rarely, when something very
  significant is changed, it can possibly break API. It may be useful
  for marketing.
  * Example: hardfork in `cardano-sl`.
* `Min` is minor version, changes when new features are added,
  but they don't break API
  and are not so important from marketing point of view.
  * Example: add new endpoints to server.
* `Fix` is fix version, can be changed only because of bugfix
  or when something minor is added, insignificant from end-user perspective.
  * Example: fix compilation with old compiler, export `X` from module `Y`.

Versions with `Fix = 0` are branched out from `master`. Versions with `Fix > 0`
(say, `1.2.3`) are branched out from versions with same `Maj.Min` and
smaller `Fix` (`1.2.3` s branched out from `1.2.2`).

Tags have names `vA.B.C` (e. g. `v1.2.3`).

### Branches and versions

Name of release branch must be formed as `PROJECT_NAME-MAJOR_VERSION-MINOR_VERSION`,
where `PROJECT_NAME` is a full project name, and `MAJOR_VERSION` and `MINOR_VERSION`
are major and minor versions respectively.
For example, `0.2.0` release of `cardano-sl` project
must live in `cardano-sl-0.2` release branch. Release `0.2.5` as well
(tags should be set appropriately).

Please note that actual version of project is defined by project settings,
not by release branch name. So we must keep this correspondence. For example,
there's `cardano-sl.cabal` file for Cardano SL project, and this file contains
`version` parameter. So when we release `0.2.0` version, we must change value
of this parameter to `0.2.0`.

Please keep the value of `version` parameter in the `master` branch actual
as well.

## CHANGELOG

We use `CHANGELOG.md` file to inform about project changes. Every new
release must be described in the corresponding section.

Each section contains 3 subsections:

1. `News` - what new features were added;
2. `Improvements` - what parts of the project were improved;
3. `Bug fixes` - what bugs were fixed.

Each subsection contains a list of items. Item can contain high-level info
(i.e. "Memory consuming was decreased from 10 MB to 8 MB") as well as low-level info
(i.e. "Bug [CSL-234] fixed"). When we're talking about low-level info with technical
details, we may refer to GitHub Issues, YouTrack Issues etc.

For example:

```
CHANGELOG:

0.2.1
    News:
	* Feature [CSL-987 Ouroboros protocol commitment changes].
	* etc.

    Improvements:
	* The value of TPS increased from 10 ot 50.
	* etc.

    Bug fixes:
	* Bug [CSL-772 Internal error: we have confirmed block version which doesn't satisfy 'canBeAdoptedBV'] fixed.
	* Bug [773 Thread blocked indefinitely in an STM transaction] fixed.
	* etc.

0.2.0
    ...
```

Please keep the sections in the "from-new-to-old" order: section for the
last release is on the top.

# How do we work with issues

## Sprints

Each task is either assigned to sprint or Unscheduled

* Each sprint corresponds to particular week
* Each sprint is assigned a sequential number and name (for example _Cardano SL
#10 Sieglinde_).
* There are a number of meta-sprints:
  * _Backlog_: sprint for technical debt to put to
  * _Postponed_: for issues, which make little sense to be performed at current
moment and which are to be reviewed and picked up later, when some conditions
occur (conditions to be described in comments to issue)
* _Unscheduled_ tasks are kind of Inbox for team

## State

Each task is assigned a _State_. Developers are to transition tasks according
to their real state (and do this synchronously with actual development).

* _Open_: any issue that exists and no work is started
* _Assigned_: When work is not started, but issue is marked as reserved by
someone to do. General difference from Open is that Open task’s assignee field
is somewhat random. Person could be even aware it’s assigned to it. On the
other hand, Assigned state means issue is on person’s short TODO list.
* _In progress_
* _To Verify_: Issue is done, but for some reason needs to be verified by
another developer (e.g. Ivan). Main cases, when developer is asked to do this
explicitly (being an intern for example) or when he wants another trusted
expertise to be provided (when code is non-trivial or design decisions are
questionable). Generally, it’s a good practice. If you’re not sure in your
code, use this state.
* _To be discussed_: Further work on issue is to be processed only after some
discussions. Reason and subject of discussion to be provided in comments.
* _Postponed_: To be used when task neither can be processed nor aborted. Major
usecase is when task is blocked by something other. Reason and conditions to
eventually process task to be provided in comments. Don’t confuse with
Postponed meta-sprint: task in Postponed state contained in regular sprint is
one that would be done by say end of sprint after other tasks done.
* _Waiting for build_: Task is believed to be done by developer and is assigned
  to be included into upcoming release
* _Waiting for test_: Task was included into one of releases and waits to be
  tested by QA team.
* _Not done_: Task was tested by QA team and required functionality doesn't work.
* _Done_
* _Aborted_: Task is aborted. This means that at some point task is no more
actual. I.e. there is no need to perform this task at this point’s prospective.
Usually because conditions of task are no more applicable to current state of
project (i.e. bug is not more reproduced). Reason for task being aborted is
better be provided in comments.
* _Duplicate_: Task is already captured within scope of other task.

### State and versions

*Open*, *Assigned*, *Postponed*, *To be discussed* task may or may not be assigned a target version. Target version here is best guess of developer, when this issue would be delivered.

*In progress*, *To Verify*, *Waiting for build*, *Waiting for test* tasks must be assigned fix version, at which this task is to be or have been released, e.g. `0.2.1`.

Tasks in *Done*, *Not fixed* state should also be assigned next minor version, e.g. `0.3.0`.

## Estimation

Each task is assigned an estimation.

Estimation here is raw estimation, without considering backlog (new tasks
spawned or to be spawned) which this task would produce after being done.

To estimate task, developer has to imagine all steps and problems he would
encounter performing task. Better to provide top edge estimation (for this
reason multiplying estimation from head by some factor, 1.5 for instance).

Each task assigned to regular sprints or to Backlog meta-sprint should be
provided an estimation.

## Sprint bootstrap

At the start of sprint, it’s to be planed:

* All issues from previous sprint which are in Open or Assigned state are to be
moved to current sprint
* All issues which are in In progress, Postponed, To be discussed states are to
be assigned to current sprint in addition to previous
* Sprint is to be reviewed by team manager for all tasks which are not
necessary to be done on this sprint to be moved to next one(s). Aiming for
sprint to represent set of tasks which would be actually finished by end of
sprint.


## Boards
There are a number of [boards](https://issues.serokell.io/agiles) we use to
have easy-to-track picture of what’s happening on projects:
* Cardano-SL Core: contains CSL, TW projects
* Cardano-SL UI: contains DAE, DAEF, CSE projects
* Cardano-SL Admin: contains CSLA project

## Bug reporting and inter-team communication

It’s already happening and with time going further it would be happening even
more often, that one team discovers bug in part of system, developed by other
team. Here is workflow to report such bugs:
* Open corresponding project (DAEF for instance)
* Create new issue: Open state, no sprint or estimation assigned
* In issue’s description describe step-by-step to reproduce bug. Description
should be sufficiently informative for anyone reading this to actually
reproduce this bug and/or understand flow which lead to it.

## General agreement on YT

All issues are to be tracked in YT.

Each task, on which some work is being in progress, must be reflected by YT
issue.

All information on YT issue is tracked in comments to issue and/or description.
If there is discussion in Slack, which directly relates to issue and/or
document, this is to be attached as comment.

All tasks on current sprint are to be assigned an estimation and properly
transition according to real state



## Release managament

This section describes our release practices.

### Fields in YT

We have a number of special-purpose fields in YT for release management:

* Affected versions
   + For bug reports/testcase failures, should contain a list of versions, for which issue was approved to reproduce.
* Affected builds
   + For bug reports/testcase failures, should contain a list of builds, for which issue was approved to reproduce.
* Target versions
   + Version, within which we are to deliver the patch associated with issue.

### Release processing

Following steps should be executed to release version `X = Maj.Min.Fix`:

1. Check issues assigned to target version `X` are all in *Wait for build* state

   Issues should be moved to different version if and only if no commits were pushed to branch corresponding to `X`.

   In case some commits were pushed, some work remained, issue should be split to two (one assigned to next version).
   Commit revert may also be an option.

2. Extract list of commits from git commit history, i.e. all commits:
   * From tag of version `Maj.Min.(Fix-1)` if `Fix > 0`.
   * From tag of version `Maj.(Min - 1).0` if `Fix == 0`.

3. Compare list of issues from Git history and list of issues for version `X`

   To make them conform:
   * Change version for issues in YT
   * Revert some commits in branch

4. Build project

5. Once project built successfully:
   1. Put tag in branch for minor version.
      If `Fix == 0`, also put tag in branch for major version
   2. Mark version in YT as released
   3. Add artifact links from CI to version description

6. Issues from version to be set status *Waiting for test* and assigned to QA team lead.

7. QA team to perform consise testing of version (release note testing or regression testing, depending on case)

8. QA team to approve no blockers discovered (i.e. only minor issues/glitches remain)

9. Release to be published to end user
