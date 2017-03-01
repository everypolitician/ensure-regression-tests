# ensure-regression-tests

Ensure Pull Requests have regression tests included - shell script that runs in your Travis build

## Usage

Add the following as a custom [Travis build step](https://docs.travis-ci.com/user/customizing-the-build#Customizing-the-Build-Step).

    bash <(curl -fsSL https://github.com/everypolitician/ensure-regression-tests/raw/v0.1.0/ensure-regression-tests)
