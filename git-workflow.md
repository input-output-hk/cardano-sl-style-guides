## Workflow

![alt tag](git-flow.png)

* We use `master` branch as a main development branch.
* Only allowed team-members can push directly to master.
* Features, fixes, refactoring must be done in separate branches.
  * They are reviewed and merged after that.
* New tagged release (even minor one) is a new branch.
* If there is a release then a `tag` is created from the commit of this release
branch.
* Fixes from release branches are merged into master.

## Branches

* Branch name should fit the following template: `(type of task)/(maybe related issue number)(name)`.
  * `bugfix/dae31-fix-tx-send`
  * `feature/csl492-wallet-delegation-certs`
  * `feature/launcher`

## Commits

We follow [this guide](http://chris.beams.io/posts/git-commit/) how to write a
commit message. Also if commit is related to an issue then we add issue name in
the beggining (i.e. `[CSLD-7] Haddock integration done`).
