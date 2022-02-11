## What is Metadata?

The MDA tracks a variety of metadata on high-traffic tables. Metadata is information _about_ the data that we are sourcing and/or creating, which may include:
  * Where the data came from (source)
  * When the data was acquired
  * What the table names mean
  * What the field names mean
  * Any information about how the data was created/generated/derived
  * and more...

## When should Metadata be updated?

If you plan to expose the data set (schema + tables within the PostgreSQL RDS instance) that you are working on to _any one_ else in the organization, you need to add Metadata.

## How to update Metadata via `coriverse`

For instruction on creating and accessing MDA metadata, visit the [cori_db wiki](https://github.com/ruralinnovation/cori_db/wiki/Metadata).