__This is the CORI/RISI MDA team style guide.__

This is an evolving set of best practices for R programming on the MDA team.

---------------------------------------------

## Naming Conventions

- Variable and table names, both in code and on the database, should always be in snake_case, i.e. all lower case with underscore separation of words.
- Github repositories dedicated to a new project should begin with the prefix `proj-`. Similarly, repositories dedicated to ETL of a specific data set should begin with the prefix `data-`. All repositories should have clear, descriptive names.
- Functions are verbs. Whenever possible use a descriptive verb or action phrase to name your function (e.g. `add_column()`, rather than `column_adder()`).
- Scripts that need to be run in a specific order should be prefixed with two digits and and an underscore, e.g. `01_`, `02_` ... `10_`. Scripts numbered with a single digit will be displayed out of order in most file explorers when there are 10 or more scripts.

---------------------------------------------

## Code

- Always use `library()` to load packages. `require()` is used in a lot of MDA legacy code, and should be replaced with `library()` when it is encountered.
- Neither `library()` nor `require()` should _ever_ appear inside of functions. 
   - State package dependencies in your scripts explicitly at the top level using `library()`.
- When creating functions that call tidyverse functions, use [the embracing operator](https://www.tidyverse.org/blog/2019/06/rlang-0-4-0/#a-simpler-interpolation-pattern-with), e.g. `{{ var }}`, instead of `!!as.name(var)` or similar.  
   - Variables inside the embracing operator should always be padded with a space on either side.
- There is no such thing as over-commented code. To the extent possible, a script should be self-documenting. Be kind to your reviewers (and six-months-from-now you) and write the comments even when you're in a hurry.
- Avoid `while` loops and nested loops of any kind.  
   - While both have applications, they are rare and there are almost always cleaner solutions that are easier to understand and reason about in the long run. 
- Always use a [project-oriented workflow](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/).
- A great deal of legacy MDA code prefixes all variables with `x.`. This practice should be avoided moving forward.
- Separate tasks, such as downloading a source file and then processing that data into a usable table, should be separated into numbered scripts.
- Whitespace makes code more readable. Always pad assignment operators and equals signs with a single space on both sides. Separate commands with one or more new lines.
- The assignment operator (`<-`) is preferred to `=` for object creation, [consistent with the tidyverse style guide](https://style.tidyverse.org/syntax.html?q=assign#assignment-1).
- When in doubt about good formatting, defer to the [tidyverse style guide](https://style.tidyverse.org/index.html)

---------------------------------------------

## Tools

- If using RStudio, ensure that under `Tools >> Global Options` the option 'Restore .RData into workspace at startup' is not checked and the option 'Save workspace to .RData on exit' is set to `Never`. Using .RData to save presets and variables trades a minor convenience for major code portability headaches. __Always__ start from a fresh session. 
   - ![settings](https://user-images.githubusercontent.com/33400922/134179279-dc74ec4b-5410-482c-a838-c804021da8ec.PNG)
---------------------------------------------

## The Zen of Functional Programming (The Zen of Python +)

- Beautiful is better than ugly.
- Explicit is better than implicit.
- Simple is better than complex.
- Complex is better than complicated.
- Flat is better than nested.
- Sparse is better than dense.
- Readability counts.
- Special cases aren't special enough to break the rules.
- Although practicality beats purity.
- Errors should never pass silently.
- Unless explicitly silenced.
- In the face of ambiguity, refuse the temptation to guess.
- There should be one-- and preferably only one --obvious way to do it.
- If you find the one obvious way to do it, document it here for the sake of your coworkers and yourself in six months.
- Now is better than never.
- Although never is often better than *right* now.
- If the implementation is hard to explain, it's a bad idea.
- If the implementation is easy to explain, it may be a good idea.
- Copy and paste is the root of 42% of all evil.
- If you would copy and paste code a third time, instead write a function.
- If you would copy and paste a function for the third time in a script, instead use a functional.
- Although a loop is better than nothing.
- If you would copy a function into a new script even once, document it and add it to the coriverse.