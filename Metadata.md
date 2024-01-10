## What is Metadata?

The MDA tracks a variety of metadata on high-traffic tables. Metadata is information _about_ the data that we are sourcing and/or creating, which may include:
  * Where the data came from (source)
  * When the data was acquired
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
