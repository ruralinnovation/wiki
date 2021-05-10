## Disconnect from a database

```
library(DBI)

con = cori_db::connect_to_db("sch_layer")

# Analysis code here

DBI::dbDisconnect(con)
```

## Validate a left join

```
# check that the result of the join has the same number of rows as the left hand side of the join
# If the numbers are identical, the join was performed on a unique key or set of keys

new_df = left_join(old_df1, old_df2, by = 'key_var')
stopifnot(nrow(old_df1) == nrow(new_df))

```