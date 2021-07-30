## This is the CORI/RISI MDA team style guide. This is an evolving document.

- Always use `library()` to load packages. `require()` is used in a lot of MDA legacy code, and should be replaced with `library()` when it is encountered.
- Variable and table names should always be in snake_case, i.e. all lower case with underscore separation of words.
- When creating functions that call tidyverse functions, use [the embracing operator](https://www.tidyverse.org/blog/2019/06/rlang-0-4-0/#a-simpler-interpolation-pattern-with), e.g. `{{ var }}`, instead of `!!as.name(var)` or similar. Variables inside the embracing operator should always be padded with a space on either side.
- There is no such thing as over-commented code. To the extent possible, a script should be self-documenting. Be kind to your reviewers (and six-months-from-now you) and write the comments even when you're in a hurry.
- Avoid `while` loops and nested loops of any kind. While both have applications, there are often cleaner solutions that are easier to understand and reason about in the long run.
