# This is the CORI/RISI MDA team style guide. This is an evolving document.

## Code

- Always use `library()` to load packages. `require()` is used in a lot of MDA legacy code, and should be replaced with `library()` when it is encountered.
- When creating functions that call tidyverse functions, use [the embracing operator](https://www.tidyverse.org/blog/2019/06/rlang-0-4-0/#a-simpler-interpolation-pattern-with), e.g. `{{ var }}`, instead of `!!as.name(var)` or similar. Variables inside the embracing operator should always be padded with a space on either side.
- There is no such thing as over-commented code. To the extent possible, a script should be self-documenting. Be kind to your reviewers (and six-months-from-now you) and write the comments even when you're in a hurry.
- Avoid `while` loops and nested loops of any kind. While both have applications, there are often cleaner solutions that are easier to understand and reason about in the long run. A good rule of thumb: `while (TRUE){ avoid_while_loops() }`
- Always use a [project-oriented workflow](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/). Specifically,
    - If the first line of your R script is `setwd("C:\Users\me\path\that\only\I\have")`, I will come into your office and SET YOUR COMPUTER ON FIRE

## Naming Conventions

- Variable and table names, both in code and on the database, should always be in snake_case, i.e. all lower case with underscore separation of words.
- Github repositories dedicated to a new project should begin with the prefix `proj-`. Similarly, repositories dedicated to ETL of a specific data set should begin with the prefix `data-`. All repositories should have clear, descriptive names.
- Scripts that need to be run in a specific order should be prefixed with two digits and and an underscore, e.g. `01_`, `02_` ... `10_`. Scripts numbered with a single digit will be displayed out of order in most file explorers when there are 10 or more scripts.
