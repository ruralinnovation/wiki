# Welcome to the coriverse wiki!

The coriverse is an evolving attempt to develop a standard set of best practices for the MDA team, centralize important or useful functions into an easily accessible location, and manage package dependencies for our scripts.

## Installation

`coriverse` is a metapackage, allowing us to conveniently install component packages that address different pieces of the MDA workflow. To install, use the following steps:

1. Set an environment variable called `GITHUB_PAT` using `Sys.setenv(GITHUB_PAT = 'MY_TOKEN_HERE')`. Instructions for creating an auth token are available in [Github's documentation](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). 

2. Call `devtools::install_github('ruralinnovation/coriverse', ref = 'main')`. `install_github()` will look for the environment variable GITHUB_PAT, which will allow you to install the dependent private repos. Currently, adding `ref = 'main'` is required, as by default `install_github()` looks for the branch `master`, which does not exist.

# Philosophy

### Functions are verbs
Whenever possible use a descriptive verb or action phrase to name your function.

```
# Good
add_column()
download_from_url()

# Bad
column_adder()
url_data()
```

### Snakes are better than camels

Prefer snake_case to camelCase and period separation in function and variable names.

### library() and require() should never be used in a function

If you absolutely need to check if a package is installed use `requireNamespace(quietly = TRUE)` in an if condition. Otherwise use `@import`, `@importFrom` and `::` notation to ensure that your dependencies are met.