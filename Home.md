# Welcome to the coriverse wiki!

The coriverse is an evolving attempt to develop a standard set of best practices for the MDA team, centralize important or useful functions into an easily accessible location, and manage package dependencies for our scripts.

# Installation

`coriverse` is a metapackage, allowing us to conveniently install component packages that address different pieces of the MDA workflow. To install, use the following steps:

0. Ensure you have `remotes` 2.4.0 or greater installed (current version as of June 2021). Use `install.packages('remotes')` to get the latest version.
1. Set an environment variable called `GITHUB_PAT` by running `Sys.setenv(GITHUB_PAT = 'MY_TOKEN_HERE')`, replacing MY_TOKEN_HERE with a valiud Github personal access token. Instructions for creating a Personal Access Token are available in [Github's documentation](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). Your PAT only needs `repo` permissions (the first section of options when creating a PAT).

2. Call `remotes::install_github('ruralinnovation/coriverse')`. `install_github()` will look for the environment variable GITHUB_PAT, which will allow you to install the dependent private repos. 

_For instructions on connecting to the database using the coriverse, see the [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki)_

---

# Development Process for New coriverse Functions

0. Create a branch of the appropriate coriverse package repo
1. Write a function. All functions from external packages should reference the package with :: syntax (e.g. dplyr::filter())
2. Save the function in the R folder of the appropriate coriverse package repo in your branch. The file name should match the function name.
3. Insert a roxygen skeleton (CTRL + SHIFT + ALT + R in RStudio)
4. Update the title, documentation of the parameters, and the return value. Add an `@import` tag for each package the function depends on. If the function uses only one or two functions from an external package, use an `@importFrom` tag for each function.
5. If the packages the function depends on do not appear in the Imports field of the DESCRIPTION file, add the package name(s) there
6. Run `devtools::document()`
7. Run `devtools::check()`
8. If the check passes with no errors, warnings, or notes, push to your branch. Otherwise, resolve errors, warnings, and notes.
9. Open a pull request and tag Matt R for review.
