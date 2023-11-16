
🏗️ This page is under construction! 🏗️
=======================================

------------------------------
## Onboarding team 

### First in pg_admin:

1. right clic on `Server` -> Register -> Server
2. New windows pop up:
 * `Name`: cori-ad
 * In the Connection tab
    `Host name/address`: cori-risi-ad-postgresql.c6zaibvi9wyg.us-east-1.rds.amazonaws.com
* Username: your username (your email)
* Password: your password
  (Toggle `save it` if you need it)
* What do you have in Parameters? if not add SSL mode as prefer

Open the "Query tools" and enter:
`ALTER USER your_username WITH PASSWORD 'my_secret_pwd';`

### Second in R
```r
remotes::install_github("ruralinnovation/cori.db")
packageVersion("cori.db")
cori.db::set_db_credentials(your_username, my_secret_pwd)
# Restart the R session 
```


-----------------------------
## Database Migration 2023-03-10
#  Moving our DB

## Goals: 

- Reduce the size (50% ? we can always increase)
- Upgrade Postgres version (15)
- Upgrade PostGIS version (3.X)
- Defining Roles/Users
- Split DB
- List potential Challenge


## Roles and Users

Do we have other people outside of the team that need access?
 
Roles we should considers (not exhaustif): 

1. Bots/software groups
     - shiny for shiny app
     - github action ?
     - other bots that need permissions? 
2. Humans groups
     - Reader/writers
     - Admins 
     - Outside of team

## Splitting the DB

So far I think we are using the DB for:

- Specific projects: share data between teams members, allow software to access it
- Storing reusable data (example TIGER)
- A bit of Archives

Question about the lifecycle of data: 

- It can start as a project then when do we decide that it either need to be stored or moved to be reused? 
- Do we have a list of data that we need for all the projects: 
    - geometry (with specific years)
    - ACS
    - BEA?
    - BLS? 
    - Broadband? 

The [list of Drew](https://rpubs.com/drewrose/data_documentation) is a great start how can we improve it? 

I think it is outside of the scope of migrating DB but we should at least link the two and we should add a specific ticket/process on how we can build a better version of it.  

What is important is how long should we keep data for every source. If I am correct we are tracking the DB with a shiny app that target the metadata schema. In my opinion it is a good idea one question I am unclear is when do we update metadata: right after writing anything? after the data is in "production" ? (other option). When we are storing past data we should think who do we clean metadata.

### splitting options?

Currently we just have one DB and multiple schemas.

I will divide with at least: 

- projects  
    - schema for specific projects  
    - staging place at schema level 
    - can be called by bots 
    - no limitation in writing/reading
- sources 
    - mostly some raw data could be redundant with data 
    - can't be called by bots
    - more limited writing 
- data   
    - Data that we need frequently and has been processed by us 
    - Can be called by bots?
    - no limitation in writing/reading

This three seems to have different lifecycle (ie when we update them/store them/delete part of them)


# list of schemas and tables that can be in prod env.

**Rules**: 

data in prod must have metadata before pushing into prod. 

Metadata is: 

* required:
    - A readme.md in a repo with description of data (dimensions and what is a "row") and the above (should be at the schema )  
    - "cookbook" at tables -> `metadata` 
    - Sources (link, date, last_updated)  
    - Licenses (PD, MIT, Apache 2 etc )
    - "executive summary description": one liner providing a quick overview of what the data is 
      
    
* optional:
    - s3 bucket with raw
    - Codes that produce table
    - validation tests   
        * summary of validation tests  
        * testing `metadata` columns 
TODO stuff: 

- [ ] build metadata for broadband resiliency CH tools
- [ ] dump and restore it 
- [ ] Some clean up to do with bb schema 
- [ ] Figure out what do we do when Olivier update FCC data
- [ ] Find a workflow data-dev -> check stuff -> move prod -> archive old stuffs
- [ ] metadata should also include being in prod or dev or something like that 
- [ ] discussion about WIM schema (should it point to dev or prod? for the win change dbname argument so it target it)

- [ ] Organize a bit what we need inside of bcat, keeping in mind that we also could want that in data prod
- [ ] very low priority build f477 last one and having code for it
- [ ] drop previous data-dev
- [ ] drop previous data when API is targeting correct DB


1. What we want inside of data-prod 
    - [ ] ~~bcat~~
    - [ ] sch_proj_climate (time to restore estimate?)
    - [x] metadata
    - [ ] sch_broadband  (everything can be in dev but in prod should only be fcc NBM) (time to restore estimate?)
    - [x] rii_diagnostics
    - [x] higher ed (everything in dev then in prod: cip_soc_xwalk, ipeds_inst_characteristics, ipeds_completions, total_degrees_by_inst  )

## TED 

shinyapp: 
- entry point app.R
- it is unsing cori.apps for component 
- it is using doctR and doctR needs fonts
- renv.lock is used by shinyapps.io to download the correct packages and version
- it needs core_data




