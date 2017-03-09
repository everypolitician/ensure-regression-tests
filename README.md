# ensure-regression-tests

Add `ensure-regression-tests` to your Travis build to run tests *twice* when
you create a pull request: once on the branch you’ve been working on, and once
on the branch you’re targeting (commonly, this will be `master`). The tests on
your branch *must pass* (of course)... but tests on the target branch *must
fail*.

Yes, Travis will reject your pull request if the **tests pass** on the target
branch.

There’s one useful exception: see the details below.

`ensure-regression-tests` is implemented as a shell script, which you simply
instruct Travis to use. You don’t need to install anything.

## Usage


Add the following as a custom [Travis build step](https://docs.travis-ci.com/user/customizing-the-build#Customizing-the-Build-Step).

    bash <(curl -fsSL https://github.com/everypolitician/ensure-regression-tests/raw/v0.1.0/ensure-regression-tests)

This will run v0.1.0 of the script (see [all versions available](https://github.com/everypolitician/ensure-regression-tests/releases)).

If you want to use a specific version of the script, or perhaps always want the
latest one, be explicit in the URL you supply to `curl`. That is, you can use
the URL to a specific git commit SHA1 or tag within this
`ensure-regression-tests` repo. For example, to execute the most recent version
from the `master` branch (rather than being locked to a specific commit or release):

    bash <(curl -fsSL https://github.com/everypolitician/ensure-regression-tests/raw/master/ensure-regression-tests)

You can see this in use in
[this live example](https://github.com/everypolitician/viewer-sinatra/blob/6058146aa548cde2f7db9cc98ed564e01577e8ff/.travis.yml#L13).

## Details

Broadly speaking there are three situations where this plays out:

### New feature branch

If you’re adding a new feature, you’d expect to add new tests for that new
feature. You’d also expect those to fail because by definition the feature your
tests are testing isn’t on the target branch (yet) — if it is, you’re not
adding a new feature, you’re refactoring a new one (see below).

### Bug-fix branch

If you’re fixing a bug, that bug snuck in despite the tests all passing on the
target branch. So you’ve certainly got to add new tests to catch this bug on
your (pull request’s) branch, and those tests must fail on the target branch
(because if they didn’t, it wouldn’t be a bug, would it?). So
`ensure-regression-tests` ensures you not only fix the bug, but that Travis
will test for it now and from here on. Beautiful regression testing; that bug
is never going to sneak back in.

### Refactor branch

Here’s the override case. If you’re just refactoring — you’re not adding a
feature, you’re not fixing a bug — then you don’t want to add new tests: you
emphatically want to show that you haven’t broken the existing ones.

So in this case the script shouldn’t insist on failures on the target branch.
This is only OK because you’re explicitly refactoring.

Make this happen by explicitly including the word `refactor` somewhere in your
branch name. This works because `ensure-regression-tests` will back off from
any branches so named.


