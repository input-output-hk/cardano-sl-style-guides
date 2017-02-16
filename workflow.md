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
* Fixes from release branches are merged into master.

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

## Versions

Versions are denoted with 3 numbers: A.B.C.

* `A` is major version which changes rarely, when something very
  significant is changed, it can possibly break API. It may be useful
  for marketing.
  * Example: hardfork in `cardano-sl`.
* `B` changes when new features are added, but they don't break API
  and are not so important from marketing point of view.
  * Example: add new endpoints to server.
* `C` can be changed only because of bugfix or when something minor is
  added which is not seen by end-users.
  * Example: fix compilation with old compiler, export `X` from module `Y`.

Versions with `C = 0` are branched out from `master`. Versions with `C > 0`
(say, `1.2.3`) are branched out from versions with same `A.B` and
smaller `C` (`1.2.3` s branched out from `1.2.2`).

Tags have names `vA.B.C` (e. g. `v1.2.3`).

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
* _Done_
* _Aborted_: Task is aborted. This means that at some point task is no more
actual. I.e. there is no need to perform this task at this point’s prospective.
Usually because conditions of task are no more applicable to current state of
project (i.e. bug is not more reproduced). Reason for task being aborted is
better be provided in comments.
* _Duplicate_: Task is already captured within scope of other task.

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

