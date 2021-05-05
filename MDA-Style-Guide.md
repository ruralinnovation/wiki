This is the CORI/RISI MDA team style guide. This is an evolving document.

- Always use `library()` to load packages. `require()` is used in a lot of MDA legacy code, and should be replaced with `library()` when it is encountered.
- Variable and table names should be in snake_case
- When creating functions that call tidyverse functions, use [the embracing operator](https://www.tidyverse.org/blog/2019/06/rlang-0-4-0/#a-simpler-interpolation-pattern-with), e.g. `{{ var }}`, instead of `!!as.name(var)` or similar. Variables inside the embracing operator should always be padded with a space on either side.

