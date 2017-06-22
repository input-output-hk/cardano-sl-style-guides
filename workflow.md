# How we develop software

## Workflow

![alt tag](git-flow.png)

* This workflow applies to all projects connected to Cardano SL,
  including libraries, clients, etc.
* We use `master` branch as the main development branch. In some
  traditional workflows this branch is called `develop`. This branch is
  semi-stable.  It means that this branch must compile, tests must pass,
  it shouldn't have trivial errors or incomplete features. For instance,
  running cardano-sl nodes shouldn't yield `notImplemented` errors. If
  stability of `master` is jeopardized, a hot-fix must be pushed.  At
  the same time, it is allowed to have known bugs in this branch.  Of
  course we should try to avoid it, but if someone pushed something to
  this branch and then it turned out there is a bug which is hard to
  fix, it doesn't mean that someone must fix it ASAP. So please don't
  expect `master` to always work.
* CI builds should be green across the platforms for PR to get merged
  into `master`. Sometimes, however, one wants to hand off an
  implemented feature with a reasonable trust in its correctness. In
  such case it should be announced that the work on the issue is
  finished, a PR created (but not merged). If that is done, other teams
  who were blocked by this issue can start working on their tasks that
  depend on this one. It's possible to spin up a testing cluster for a
  particular commit hash. It is expected that testing clusters will be
  span up in cases when such dangling PRs are created.
* `master` and release branches are protected. There can't be force
  pushes and all changes must be done via PR. PR must be approved by
  someone else and all CI checks must pass. Administrators are allowed
  to push directly to these branches but only in **exceptional cases
  when it is strictly necessary**.
* Features, fixes, refactoring must be done in separate branches.
* If there is a release, a `tag` is created from the commit
  corresponding to this release.
* We use release branches for GitHub Releases page, please see
  [one](https://github.com/input-output-hk/cardano-sl/releases) for
  `cardano-sl` project.
* Release branches must be merged into `master` every time changes
  happen in the former (single commit or a small group of related
  commits which happened in a short period of time). It's important,
  because otherwise `master` will eventually differ from release branch
  too much and nobody will be able to merge it. You can pass `-s ours`
  to `git merge` to discard all changes if it's desirable. Such merges
  must be done via pull requests too. `master` is never merged into
  release branch.
* `cardano-sl` project uses a more complicated workflow with `-mid`
  and `-rc` branches. `-mid` branches are intended to be used by other
  projects which depend on `cardano-sl`. `-rc` branch is created
  prior to release. These branches are protected too. It's described
  in more detail [below](#special-workflow-for-cardano-sl-project).
* If a project depends on a concrete version (specified as a commit
  hash) of another project, this version must be in **protected**
  branch. It ensures that commit with given hash will not disappear.

## Branches

* Generally branch name should fit the following template:
  `(nickname)/(maybe related issue number-)(very brief
  description)`. If nickname is not applicable to a branch (most likely
  because multiple people works there) it can be skipped. Examples:
  * `akegalj/dae31-fix-tx-send`
  * `csl492-wallet-delegation-certs`
  * `gromak/launcher`

## Commits

We follow [this guide](http://chris.beams.io/posts/git-commit/) how to
write a commit message. Also if commit is related to an issue then one
must add the issue identifier at the beggining (e. g. `[CSL-659] Introduce
Pos.Communication.Protocol`). Issue identifier is not necessary only
in few cases, specifically:
* in merge commits;
* if commit doesn't modify AST of the program;
* if commit fixes compilation (it usually shouldn't happen because of
  CI, but life is hard).

In other cases please create an issue for your changes even if they
are small. That's necessary to track what has been done between
different versions.

## Pull requests

When you finish your work in your branch, you should create a pull
request to your project. Create a PR to `master` branch or another
appropriate branch. Please assign a reviewer (if you don't know a
person then project manager would do it himself). You can merge your
own PR only if it's approved and all mandatory CI checks pass.

Please describe in PR what you have done, if it is tested or not, any
additional information.

You can also create a PR before your work is finished, but in this
case please add a `wip` label to it.

## Merging Pull requests

After review by another team member, the teammate merging the PR
should merge `master` into the pull request's branch to resolve
conflicts and then the PR branch into `master`, with the `--no-ff`
option enabled. Usually it's enough to just press `Merge` button on
github, as long as all checks pass and there are no conflicts.

Generally, the pull request's branch should be merged into master,
but if it is clear that rebasing this branch onto master is better
(for instance, it is a trivial change which doesn't deserve branching)
then a rebase can be done instead. Use your judgement.

## Versions

Versions are denoted with 3 numbers: `Maj.Min.Fix`. General guidelines
are described below.

* `Maj` is major version which changes **rarely**, when something very
  significant is changed, it can possibly break all kinds of API it
  provides. It may be useful for marketing. Decision whether to change
  `Maj` version should be made by competent people.
  * Example: input endorsers are added to `cardano-sl`.
* `Min` is minor version, which changes when new features are added as
  a result of usual development. It can break API. Note that there are
  multiple different APIs, for example: network messages, library
  functions, protocol logic, etc. All these APIs can be broken at
  early stage of project, but after project becomes stable we should
  try to keep APIs stable too.
  * Example: add new endpoints to a server.
* `Fix` is fix version, can be changed only because of bugfix or when
  something minor is added, insignificant from end-user
  perspective. The only purpose of it is to maintain existing
  release. If change is not necessary for this release, then it should
  go to `master`.
  * Example: fix compilation with old compiler, export `X` from module
    `Y`, add logging somewhere to facilitate future debugging.

Versions with `Fix = 0` are branched out from `master`. Versions with
`Fix > 0` live in the same branch as versions with same `Maj.Min` and
smaller `Fix`.

Tags have names `vA.B.C` (e. g. `v1.2.3`).

### Branches and versions

Name of release branch must be formed as
`PROJECT_NAME-MAJOR_VERSION-MINOR_VERSION`, where `PROJECT_NAME` is a
full project name, and `MAJOR_VERSION` and `MINOR_VERSION` are major
and minor versions respectively.  For example, `0.2.0` release of
`cardano-sl` project must live in `cardano-sl-0.2` release
branch. Release `0.2.5` as well (tags should be set appropriately).

Please note that actual version of project is defined by project
settings, not by release branch name. So we must keep this
correspondence. For example, there's `cardano-sl.cabal` file for
Cardano SL project, and this file contains `version` parameter. So
when we release `0.2.0` version, we must change value of this
parameter to `0.2.0`. If project consists of multiple packages which
conceptually form the same package, all of them should have the same
version.

Please keep the value of `version` parameter in the `master` branch actual
as well (corresponding to the latest release).

### Special workflow for cardano-sl project

Table below illustrates a special workflow used for cardano-sl project
with respect to branches and deployment clusters.

| Branch             | Deployment  | Purpose                                        |
| -----------------  | ----------- | ---------------------------------------------- |
| master             | csl-testing | Testing current master (by devs)               |
| cardano-sl-X.Y-rc  | csl-qa      | QA testing of public release candidate (qanet) |
| cardano-sl-X.Y     | csl-testnet | Public release (testnet)                       |
| cardano-sl-X.Y-mid | ——————————— | Used by other projects (e. g. explorer)        |

Flow should be following:
1. When version is being wrapped up, `master` should be deployed to
   `csl-testing` cluster, problems arising right away should be
   resolved.
2. After successful deployment on `csl-testing`:
    * `vX.Y.0` tag should be set (in `master`);
    * `cardano-sl-X.Y-rc` should be branched out of `master`;
    * `cardano-sl-X.Y-rc` should be deployed on `csl-qa` cluster;
    * all bugs discovered should be fixed in `-rc` branch, `Fix` version
	is increased for each fix.
3. After `X.Y` version is polished and is ready to be published (it's
`vX.Y.Z` already):
    * `cardano-sl-X.Y-rc` should be renamed to `cardano-sl-X.Y`;
    * `cardano-sl-X.Y-mid` is created and points to the same commit as
      `cardano-sl-X.Y`;
    * all projects depending on the `cardano-sl-X.{Y-1}-mid` branch
      should start migration to the new `-mid` branch or
      `cardano-sl-X.{Y-1}`;
    * configuration files (e. g. compile-time constants) should be
      changed;
    * `vX.Y.Z` should be deployed on `csl-testnet` cluster;
    * tada.iohk.io, daedaluswallet.io should be updated;
    * after all projects migrate from `cardano-sl-X.{Y-1}-mid` this
      branch should be removed.

Rules for `-rc` branch are same as for release branch, specifically:
* `-rc` branch must be merged into `master` after every change;
* `master` is never merged into `-rc` branch;
* `-rc` branch must be protected.

### Interaction between cardano-sl and projects dependent on it

Some projects (e. g. `cardano-sl-explorer`, `daedalus`) depend on
`cardano-sl`. There may be more such projects in future. In order to
facilitate interaction with `cardano-sl` we use special branch `-mid`
mentioned above. Let's call project which depends on `cardano-sl`
ProjectX (it may be explorer or daedalus).

* ProjectX must depend only on version of `cardano-sl` from release
  branch or `-mid` branch, not `-rc` branch. This rule is imposed
  because only these branches are generally stable and
  well-tested. `-rc` branch corresponds to a version which is not
  properly tested yet, hence ProjectX should not depend on it.
* ProjectX should always be launched with the same version of
  `cardano-sl` as the version it depends on (on the code
  level). That's because different versions of `cardano-sl` may be
  incompatible in some way and it may lead to different (inconsistent)
  behavior of `cardano-node` and `cardano-explorer`.
* ProjectX may use whatever version of `cardano-sl` it needs to
  use. However, it's encouraged to provide an easy way to see which
  version of `cardano-sl` is used in given version of ProjectX.
* If ProjectX developer wants to change something in `cardano-sl`
  version `X.Y.Z` (which is used by some version of ProjectX, say
  `A.B.C`) they should make a PR to `cardano-sl-X.Y` branch, wait
  until it's merged and then use newer version in ProjectX (version
  `A.B.C`).
* `master` branch of ProjectX usually should depend on the latest
  `cardano-sl-X.Y-mid` branch of `cardano-sl`. Exceptions: migration
  period when new version has been just released (in this case
  `cardano-sl-X.{Y-1}-mid` can be used); situations when `cardano-sl`
  contains something new that is not ready for integration into
  ProjectX (in this case older release of `cardano-sl` can be used).
* If ProjectX developer wants to change something in `cardano-sl` for
  ProjectX's `master` they should make a PR to the latest
  `cardano-sl-X.Y-mid` of `cardano-sl`.
* Latest release of `cardano-sl` (branch `cardano-sl-X.Y`) should be
  frequently merged into `cardano-sl-mid`.
* `cardano-sl-X.Y-mid` should be frequently merged to `master`.

Notice that usually there will be only one `cardano-sl-X.Y-mid` branch
in `cardano-sl`. However, right after new version of `cardano-sl` is
released there will be some migration period during which `master` of
ProjectX will depend on old version of `-mid` and there will be work
in progress to start using newver version. So sometimes we need more
than one `-mid` branch and for this reason we put version into its
name.

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

# How we work with issues

## Sprints

Each task is either assigned to sprint or Unscheduled

* Each sprint corresponds to particular week
* Each sprint is assigned a sequential number and name (for example _Cardano SL
#10 Sieglinde_)

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

*Open*, *Assigned*, *Postponed*, *To be discussed* task may or may not
be assigned a target version. Target version here is best guess of
developer, when this issue would be delivered.

*In progress*, *To Verify*, *Waiting for build*, *Waiting for test*
tasks must be assigned fix version, at which this task is to be or have
been released, e.g. `0.2.1`.

Tasks in *Done*, *Not fixed* state should also be assigned next minor
version, e.g. `0.3.0`.

## Estimation

Each task is assigned an estimation.

Estimation here is raw estimation, without considering backlog (new tasks
spawned or to be spawned) which this task would produce after being done.

To estimate task, developer has to imagine all steps and problems he would
encounter performing task. Better to provide top edge estimation (for this
reason multiplying estimation from head by some factor, 1.5 for instance).

Each task assigned to regular sprints should be
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
