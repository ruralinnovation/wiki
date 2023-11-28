# Ansible in the coriverse

Ansible is an essential tool for managing our data infrastructure. Initially it was only employed to create new linux user accounts during MDA onboarding (see [legacy](https://github.com/ruralinnovation/ansible/tree/main/playbooks/legacy) playbooks), but ever since the great RDS Migration of 2023, [`ansible`](https://github.com/ruralinnovation/ansible/) is the main way that we provision, save and restore data in bulk to the various databases (see [PostgreSQL-RDS-Managment](PostgreSQL-RDS-Managment.md) section in the [wiki](https://ruralinnovation.github.io/wiki/)).

Ansible playbooks are used to manage users on the team's shared EC2 instance and databases instance within our RDS cluster.  
 
`inventory/`: stores our ansible hosts (CORI/RISI R server) see 1) for shell env. variable. 

`playbooks/`: contains various playbooks:  

`playbooks/legacy/`: playbooks for managing EC2 instance and adding users. Adding new users worked in January 2023. The old readme was also added here. 

`playbooks/basic/`: provides a playbook to test Ansible and seeing if the connection to some target host works. 

`playbooks/cori-risi-ad-postgresql/`: playbooks to connect to the Active-Directory-secured RDS cluster and manage database instances and users.

`playbooks/cori-risi-old-db/`: playbooks that were used to connect to the now defunct "old" database (in a terminated RDS cluster).

`playbooks/kerberos/`: playbook to verify the ability to authenticate with Active Directory

`playbooks/postgresql/`: before running playbooks against the database instances on the new RDS cluster we tested them on the local PostgreSQL instance running on our shared EC2 instance.

`playbooks/queries/`: SQL helper/utility scripts.

`package.json`: npm is also used here as a way to store some procedures and save some time and brain space (see as an example: `npm run clean`).


## Set up Ansible

So far, this works (October 2023):

1) Assign appropriate values for the ssh user and private key file to the  shell environment variables, `ANSIBLE_SSH_USER` and `ANSIBLE_SSH_PRIVATE_KEY_FILE`:

```shell
# export your name from EC2 instance
export ANSIBLE_SSH_USER=<USER_NAME>
# check ls ~/.ssh to know which key use
export ANSIBLE_SSH_PRIVATE_KEY_FILE=<~/.ssh/SSH_PRIVATE_KEY_FILE>
```
2) Run `npm install` _or_ manually write a simple, *local* hosts file (`hosts`): 
```
   [cori_risi_r_server]
   18.235.239.47
```
3) Run the basic ansible ping command *with* command line arguments for the name of the ssh user and the path to the ssh private key file:
```shell
ansible cori_risi_r_server -m ping -i hosts --extra-vars "ansible_ssh_user=$ANSIBLE_SSH_USER" --extra-vars "ansible_ssh_private_key_file=$ANSIBLE_SSH_PRIVATE_KEY_FILE"
```

You should be prompted with:

```
18.235.239.47 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```
5) Alternatively, this repo also includes a `hosts.yml` file in the inventory subdirectory, which can be supplied to the ansible commands with `-i inventory/hosts.yml`:
```shell
$ ansible cori_risi_r_server -i inventory/hosts.yml -a "/bin/echo hello"
```
 
<!--
## Resources: 

https://stackoverflow.com/questions/22483555/postgresql-give-all-permissions-to-a-user-on-a-postgresql-database
-->
