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
at the beggining (i.e. `[CSLD-7] Haddock integration done`).
