
---------------------

# TOC
[Connect to Postgres](#connect)

[Read Data](#read)

[Write Data](#write)

[Disconnect from DB](#disconnect)

[Validate join](#validate)

[Explore Data](#explore)

<a name="connect"/>

---------------------

## Connect to Postgres

Follow instructions on the [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki) to set up your `.Renviron` file.

```
con <- connect_to_db('sch_layer)
```

<a name="read"/>

---------------------

## Read Data

#### CSV

- Use `readr::read_csv()` for files with < 100K rows. For larger files, use `data.table::fread()`

#### Google Sheet

```

library(googlesheets4)

options(httr_oob_default = TRUE)
gs4_auth()

read_sheet("URL")

```

#### .SHP, .GPKG, or other spatial file

- `sf::st_read()`

#### ESRI Server

```
remotes::install_github('yonghah/esri2sf')

library(esri2sf)

esri2sf(url)

```

---------------------

<a name="write"/>

## Write Data

### NON-SPATIAL

#### To CSV

- Use `readr::write_csv()` for data sets of <100K rows, `data.table::fwrite()` for larger data sets

#### To Postgres 

```
DBI::dbWriteTable(con, "name", df)
```

#### Google Sheets

```
library(googlesheets4)

options(httr_oob_default = TRUE)
gs4_auth()

# make a new workbook
gs4_create("name", sheets = list(sheet_name = df, ...))

# existing workbook

write_sheet(df, ss = "workbook_url", sheet = "worksheet_name_to_write_to)
```

#### To Airtable

- Follow documentation in the [CIMS ReadMe](https://github.com/ruralinnovation/county-crm), or ask Matt R

---------------------

### SPATIAL

#### To Postgres 

```
sf::st_write(df, con, "name")
```

#### To File

```
sf::st_write(df, "path/name.gpkg")
```

#### CARTO

- `write_carto()` function on RStudio server (`/data/Github/base/functions/write_carto.R`)

---------------------

<a name="disconnect"/>

## Disconnect from a database

```
library(DBI)

con = cori.db::connect_to_db("sch_layer")

# Analysis code here

DBI::dbDisconnect(con)
```

---------------------

<a name="validate"/>

## Validate a left join

```
# check that the result of the join has the same number of rows as the left hand side of the join
# If the numbers are identical, the join was performed on a unique key or set of keys

new_df = left_join(old_df1, old_df2, by = 'key_var')
stopifnot(nrow(old_df1) == nrow(new_df))

```

<a name="explore"/>

## Explore a dataset

- Use `dplyr::glimpse()` to get an overview of large data sets (many columns)
- `head()` will work well for data with fewer columns
- For numeric variables of interest, `summary()` provides a good overview
- For categorical variables, use `group_by()` and `count()` from `dplyr` to get counts by group
- Get count of `NA` values across all columns with `lapply(df, function(c) sum(is.na(c)))`