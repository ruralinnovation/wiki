---
title: "Metadata"
execute:
  echo: true
  output: true
  message: false
  warning: false
reference-location: margin
citation-location: margin
---

# TODO: How ?

First steps...

  - we should focus first on:

    * tables in DB

    * bucket in s3

  - setup automation for that (write db)

  - see what we do when pull metadata is writing to temp/disk


## What is Metadata?

The MDA tracks a variety of metadata on high-traffic tables. Metadata is information _about_ the data that we are sourcing and/or creating, which may include:
  * Where the data came from (source)
  * When the data was acquired -> 
  * What the table names mean
  * What the field names mean
  * Any information about how the data was created/generated/derived
  * and more...

## When should Metadata be updated?

Metadata should always be created for:
  * All active source data sets (data stored in database after ETL, not necessarily raw source data).
  * All product data sets (data sets exposed in a tool or MDA-derived data sets used across projects, i.e. Climate Resiliency data)
  * Data produced for a specific (terminal) project does not necessarily require metadata, this is left to the discretion of the analyst.

Additionally, if you plan to expose the data set (schema + table within the PostgreSQL RDS instance) that you are working on to _anyone_ else in the organization, you need to add Metadata.

## When should Metadata be removed?

When source data is removed from the database.

## Who is responsible for creating and updating metadata?

Data is typically either source data or generated for specific products or projects. The MDA Data Engineer has primary responsibility for ensuring the existence of source metadata. For project or product specific data, the project or product owner has ultimate responsibility for creating metadata.

## How to update Metadata via `coriverse`

For instruction on creating and accessing MDA metadata, visit the [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki/Metadata).

## works on Metadata

### global level view

we have:
- tables in DB (data-prod / data ?), tables can support products and/or analysis or be temporary
- a schema (multiple tables) that are referencing those information (metadata)
- an R package (cori.db) that are helping updating Metadata
- someone need to either fill a csv and/or write the description for the metadata to be complete
- metadata can be a dependency on other tools
- it has a "visualizer": CORI explorer  <- dependencies on metadata function

List of specific tables: 
``` r
# [1] "acs_metadata"         "field_metadata"      
# [3] "source_metadata"      "product_metadata"    
# [5] "table_metadata"       "pipeline_diagnostics"
```

### cori.db functions and a yaml dealing with metadata

yaml (nst/params/package_params.yml) with the structure of 3 tables :
- source_metadata
- field_metadata
- table_metadata:

ie only those 3 tables are supported by cori.db 
- `create_metadata_tables()` is the first function to run for new data set 
it has the yaml as dependency, can be run with a df or without, 
by default write 3 .csv, if a df is provided it will 
return one of the csv with the column name already filled

- `update_metadata()` take con, and need field_meta, table_meta, source_meta to exist and be valid df or exit 
 the cols of the 3 must be same as yaml or stop
 if the data are already in metadata it will ask the user if he want to override it

 (Pot. improvement: it will break in a non interactive sessions)
 
 repeat the process for the 3 tables 
 
 glue produce multiple queries hence the need to Vectorize 
 
 then it deletes and append (times 3)

 - `read_db()` (using pull_metadata) by default it will check if the table is 
present in 3 tables and save a 3 csv for them with select row

 - acs metadata is updated here https://github.com/ruralinnovation/data-acs/blob/master/99_update_metadata.R
 it uses prev function update_metadata and DBI::dbWriteTable(con, "acs_metadata", codebook, overwrite = TRUE)
 be careful update_metadata is commented out in main branch

### TODO product to metadata
-  metadata in MVP state
-  update our tools in production: BEAD / CH 
- cori.utils check
- side not unsure if dplyr::add_rows should be added here

