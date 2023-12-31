
# Suggested Code Snippets

## TOC

- [Working with SQL](#sql)

- [Read Data](#read)

- [Write Data](#write)

- [Disconnect from DB](#disconnect)

- [Validate join](#validate)

- [Explore Data](#explore)

- [Visualize data on a map](#visualize-data-on-a-map)

- [Compare 2 dataframes](#compare-2-dataframes)


---------------------

<a name="sql"/>

# Working with SQL in R

### Connect to Postgres

Follow instructions on the [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki) to set up your `.Renviron` file.

```r
con <- connect_to_db('sch_layer')
```
The [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki) also documents several helper functions for DB interaction in the coriverse.

### Read SQL files

```r
query <- cori.db::read_sql('path/to/sql.file')

# read_sql() is vectorized, so you can read multiple files into a vector of queries

queries <- cori.db::read_sql(c('path/to/file1.sql', 'path/to/file2.sql'))
```

### Execute a query on Postgres (no return value)

```r
execute_on_postgres(query)

# execute_on_postgres() is vectorized, so you can execute multiple queries sequentially

execute_on_postgres(queries)

```

### Read data from a query

```r
con <- connect_to_db('schema')
dta <- DBI::dbGetQuery(con, "select * from table limit 5")
DBI::dbDisconnect(con) # important !

```

<a name="read"/>

---------------------

# Read Data

#### CSV

- Use `readr::read_csv()` for files with < 100K rows. For larger files, use `data.table::fread()`

#### Google Sheet

```r

library(googlesheets4)

options(httr_oob_default = TRUE)
gs4_auth()

read_sheet("URL")

```

#### .SHP, .GPKG, or other spatial file

- `sf::st_read()`

#### ESRI Server

```r
remotes::install_github('yonghah/esri2sf')

library(esri2sf)

esri2sf(url)

```

#### Airtable

```r
remotes::install_github('matthewjrogers/rairtable')

set_airtable_api_key('MY_KEY_HERE')

atbl <- airtable('Table Name', "BASE_ID")

dta <- read_airtable(atbl)

```
---------------------

<a name="write"/>

# Write Data

### NON-SPATIAL

#### To CSV

- Use `readr::write_csv()` for data sets of <100K rows, `data.table::fwrite()` for larger data sets

#### To Postgres 

```r
DBI::dbWriteTable(con, "name", df)
```

#### Google Sheets

```r
library(googlesheets4)

options(httr_oob_default = TRUE)
gs4_auth()

# make a new workbook
gs4_create("name", sheets = list(sheet_name = df, ...))

# existing workbook

write_sheet(df, ss = "workbook_url", sheet = "worksheet_name_to_write_to)
```

#### To Airtable

```r

remotes::install_github('matthewjrogers/rairtable')

set_airtable_api_key('MY_KEY_HERE')

# requires that table already exists with correct column names and types
insert_records()

# same requirements as insert, plus you need data with airtable record ids
update_records()

```

- [Documentation for `rairtable` package](https://matthewjrogers.github.io/rairtable)

- Also consider [Airtable Python Wrapper](https://airtable-python-wrapper.readthedocs.io/en/master/index.html), as it is somewhat faster.

---------------------

### SPATIAL

#### To Postgres 

```r
sf::st_write(df, con, "name")
```

#### To File

```r
sf::st_write(df, "path/name.gpkg")
```

#### CARTO

```r
library(coriverse)

write_carto(sf_df, 'name_of_layer_on_carto', Rto::carto())

```

---------------------

<a name="disconnect"/>

# Disconnect from a database

```r
library(DBI)

con = cori.db::connect_to_db("sch_layer")

# Analysis code here

DBI::dbDisconnect(con)
```

---------------------

<a name="validate"/>

# Validate a left join

```r
# check that the result of the join has the same number of rows as the left hand side of the join
# If the numbers are identical, the join was performed on a unique key or set of keys

new_df = dplyr::left_join(old_df1, old_df2, by = 'key_var')
stopifnot(nrow(old_df1) == nrow(new_df))

```

<a name="explore"/>

# Explore a dataset

- Use `dplyr::glimpse()` to get an overview of large data sets (many columns)
- `head()` will work well for data with fewer columns
- For numeric variables of interest, `summary()` provides a good overview
- For categorical variables, use `group_by()` and `count()` from `dplyr` to get counts by group
- Get count of `NA` values across all columns with `lapply(df, function(c) sum(is.na(c)))`

# Visualize data on a map
- `plot()` is the simplest option. Use `plot(table$geography_field)` to just see a single map.
- `ggplot()` and geom_sf() are useful when you want to see multiple layers overlayed.

    e.g.
    ```r
    ggplot() +
      geom_sf(data = county_polygon) +
      geom_sf(data = served_polygon, color = "green", fill = "green") +
      geom_sf(data = county_roads_union) +
      geom_sf(data = served_roads, color = "red")
    ```
- leaflet() is great for seeing data on top of a basemap that lets you zoom in and out, add popups, etc. The [documentation](https://rstudio.github.io/leaflet/) is pretty good.

    e.g.
    ```r
    leaflet() %>%
      addTiles() %>%
      addPolygons(data = albany_boundary, color = "black") %>%
      addPolylines(data = albany_roads, color = "yellow") %>%
      addPolylines(data = albany_served_roads, color = "red") %>%
      addMarkers(data = Carroll_County_Addresses_g %>% filter(Town_Name == "Albany"))
    ```

<a name="compare-2-dataframes"/>

# Compare 2 dataframes
(Useful when updating data and you want to see what changed)

```r
library(arsenal)
# st_drop_geometry() only needed for spatial data frames
summary(comparedf(old_table %>% st_drop_geometry(), new_table %>% st_drop_geometry()), by = "id")
```
