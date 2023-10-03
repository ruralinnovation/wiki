
🏗️ This page is under construction! 🏗️
=======================================


-----------------------------
## Database Migration 2023-03-10
#  Moving our DB

## Goals: 

- Reduce the size (50% ? we can always increase)
- Upgrade Postgres version (15, 13 ?)
- Upgrade PostGIS version (3.X)
- Defining Roles/Users
- Split DB
- Build an Ansible playbook so we can rebuild it? 
- List potential Challenge

## Postgres/PostGIS version

What are pro&cons to not go for latest stable version?

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

## Build an Ansible playbook? 

It seems it exists (https://docs.ansible.com/ansible/5/collections/community/aws/rds_module.html) and we are already using one role/task to add new users

Pro: is that it allows us to keep tract of everything and rebuild (at the infrastructure level)

Con: 
- is ansible overkill? 
    _No, not overkill and we do have an ansible client running on the R server that we currently only use for user onboarding, but could definitely be expanded to include a playbook to:_
    - Stand up a new RDS server
    - Add user roles to that database server based on R server's current user accounts
    - Migrate data/functions/schemas/tables from `cori-risi` to `<new-db>`
- I have limited knowledge of it

## Potential challenges

Our code rely on cori.db or call into db. The name of DB is hard coded in the function. We can change the function in cori.db or match the name in the next DB.  
