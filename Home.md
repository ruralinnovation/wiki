# Welcome to the coriverse wiki!

The coriverse is an R package, which is an evolving attempt to develop a standard set of best practices for the MDA team, centralize important or useful functions into an easily accessible location, and manage package dependencies for our scripts. 

---

# New Users - Data Access

---

# Installation

`coriverse` is an R metapackage, allowing us to conveniently install component R packages that address different pieces of the MDA workflow. To install, use the following steps:

0. Ensure you have :package: `remotes` 2.4.0 or greater installed (current version as of June 2021) and `usethis`. Use `install.packages('remotes');install.packages('usethis')` to get the latest version.
1. Create a GitHub token:

```r
    ## (optional, if not previously done) set your user name and email:
    # usethis::use_git_config(user.name = "YourName", user.email = "your@mail.com")
    
    ## create a personal access token for authentication:
    usethis::create_github_token() 
    ## in case usethis version < 2.0.0: usethis::browse_github_token() (or even better: update usethis!)
    ## 2023-01-25: it opens the default web browser at github PAT web page see 1.

    ## set personal access token:
    credentials::set_github_pat("ghp_...")
```
1. Set an environment variable called `GITHUB_PAT` by running `Sys.setenv(GITHUB_PAT = 'MY_TOKEN_HERE')`, replacing MY_TOKEN_HERE with the valid GitHub personal access token you previously created.   
Instructions for creating a Personal Access Token are available in [GitHub's documentation](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). Your PAT only needs `repo` permissions (the first section of options when creating a PAT).

![scope](https://user-images.githubusercontent.com/33400922/135469840-d7076fe8-4e89-49ea-aeab-0701d3d54d12.PNG)

2. Use the `install_github()` function to install the coriverse package(s), which will look for the environment variable GITHUB_PAT and will allow you to install packages from private repos. Call:
```r
    remotes::install_github('ruralinnovation/coriverse')
```
_For instructions on connecting to the database using the coriverse, see the [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki)_

---

# Database access Functions in cori.db

Go here https://ruralinnovation.github.io/cori_db/

---

# Development Process for New `coriverse` Functions

0. Create a branch of the appropriate `coriverse` package repo
1. Write a function. All functions from external packages should reference the package with :: syntax (e.g. dplyr::filter())
2. Save the function in the R folder of the appropriate coriverse package repo in your branch. The file name should match the function name.
3. Insert a roxygen skeleton (CTRL + SHIFT + ALT + R in RStudio)
4. Update the title, documentation of the parameters, and the return value. Add an `@import` tag for each package the function depends on. If the function uses only one or two functions from an external package, use an `@importFrom` tag for each function.
5. If the packages the function depends on do not appear in the Imports field of the DESCRIPTION file, add the package name(s) there
6. Run `devtools::document()`
7. Run `devtools::check()`
8. If the check passes with no errors, warnings, or notes, push to your branch. Otherwise, resolve errors, warnings, and notes.
9. Open a pull request and tag Matt R for review.
