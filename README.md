# ensure-regression-tests

Add `ensure-regression-tests` to your Travis build to run tests *twice* when
you create a pull request. Specifically, this uses the tests from the pull
request branch (that's the branch you've been working on) and runs them twice:

* once on the branch you’ve been working on (this is what you'd expect, and
  what you almost certainly did locally anyway)

* once on the branch you’re targeting (commonly, this will be `master`).

The tests on your branch *must pass* (of course)... but tests on the target
branch *must fail*.

Yes, Travis will reject your pull request if all the tests on your branch
*pass** on the target branch.

There’s one useful exception: see the details below.

`ensure-regression-tests` is implemented as a shell script, which you simply
instruct Travis to use. You don’t need to install anything.

## Usage


Add the following as a custom [Travis build step](https://docs.travis-ci.com/user/customizing-the-build#Customizing-the-Build-Step).

    bash <(curl -fsSL https://github.com/everypolitician/ensure-regression-tests/raw/master/ensure-regression-tests)

This will run the latest version of the script (from `master` in this
`ensure-regression-tests` repo). 
See [this live example](https://github.com/everypolitician/viewer-sinatra/blob/6058146aa548cde2f7db9cc98ed564e01577e8ff/.travis.yml#L13),
which shows the `bash` command in context.

If you want to use a specific version of the script, be explicit in the URL you
supply to `curl`. That is, you can use the URL to a specific git commit SHA1 or
tag within this `ensure-regression-tests` repo. For example, to execute the
`v0.1.0` release, do:

    bash <(curl -fsSL https://github.com/everypolitician/ensure-regression-tests/raw/v0.1.0/ensure-regression-tests)

See [all versions available](https://github.com/everypolitician/ensure-regression-tests/releases)).

## Details

Broadly speaking there are three situations where this plays out:

### Bug-fix branch

If you’re fixing a bug, that bug snuck in despite the tests all passing on the
target branch. This is at the heart of what `ensure-regression-tests` is
addressing:

* you’ve found a bug
* your existing tests didn’t catch that bug
* so when fixing that bug, you add a test (or tests)
* *but*... that test must fail against the previous code (which is on the
  target branch) — if it doesn’t, then your test is not actually testing what
  you think it is

In this way, `ensure-regression-tests` ensures you not only fix the bug, but
that Travis will test for it now and from here on. Beautiful regression
testing; that bug is never going to sneak back in.

### New feature branch

If you’re adding a new feature, you’d expect to add new tests for that new
feature. You’d also expect those to fail on the target branch because by
definition the feature your tests are testing isn’t there (yet) — if it is,
you’re not adding a new feature, you’re refactoring an existing one (see below).

### Refactor branch

Here’s the override case. If you’re just refactoring — you’re not adding a
feature, you’re not fixing a bug — then you shouldn’t be adding new tests here
anyway. More importantly, you want to know your refactoring hasn’t broken
anything, so you _need_ the tests on your refactor branch to pass on the target
branch too. That’s contrary to what `ensure-regression-tests` normally enforces.

So, if you’re refactoring, you should deliberately suppress
`ensure-regression-tests`, so Travis won’t reject your pull request for having
tests that pass on the target branch.

Make this happen by explicitly including the word `refactor` in the name of
your branch.

This works because `ensure-regression-tests` will politely back off if the pull
request branch on which it’s been invoked contains `refactor` in its name.
