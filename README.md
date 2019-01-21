# mybkp

Lots of systems and platforms use MySQL for their database needs.
The danger rises when they run as a single instance, with no redundancy nor backup strategies.
This script aims to help users to create regular backups of their databases.

## Usage

```sh
./mybkp [config_files]
```

Example:

```sh
./mybkp config.ini config2.ini
```

The following `user cron` runs every day at 4AM.

```cron
0 4 * * * php /path/to/mybkp config.ini
```

Output example:

```
Database
========
 Connection: gym_admin@localhost
     Server: 10-MariaDB (Debian) x86_64 debian-linux-gnu
    Schemas: 11
     Tables: 468
       Size: 876 MB
        SSH: gyms

Backup
======
    Schemas: 3
     Tables: 3
       Size: 0.3 MB
     Output: /mybkp/dumps/localhost/
 Disk space: 58484 MB
       Mode: full
   Compress: Yes

> BACKING UP SCHEMA cliente_001 TABLE actividad - OK
    /mybkp/dumps/localhost/cliente_001/20190119/actividad.sql.gz [0.1 MB]

> BACKING UP SCHEMA cliente_002 TABLE actividad - OK
    /mybkp/dumps/localhost/cliente_002/20190119/actividad.sql.gz [0.1 MB]

> BACKING UP SCHEMA cliente_003 TABLE actividad - OK
    /mybkp/dumps/localhost/cliente_003/20190119/actividad.sql.gz [0.1 MB]
```

## Requirements

Software:

* PHP 7+
* MYSQL CLIENT 5+
* GZIP
* SSH [1]

Credentials

* MySQL user with access to information_schema
* SSH user with access to MySQL binaries [1]

[1] Required if connecting through an SSH session

## Recommendations

The config file accepts passwords but not certificates, it is suggested to setup automatic login for MySQL and SSH.

* Store the MySQL password in an option file. [Reference](https://dev.mysql.com/doc/refman/8.0/en/password-security-user.html).
* SSH configuration file. [Reference](https://linux.die.net/man/1/ssh-copy-id).


## Configuration

```ini
; This is the default configuration for mybkp

[dump]

; Databases to ignore.
ignore_schemas[] = example
ignore_schemas[] = mysql

; The databases to dump. If not specified all accesible databases are dumped.
; Wilcard * (asterisk) can be used to match partial names
;dump_schemas[] =

; Tables to ignore. * (asterisk) wildard can be used to match partial names.
ignore_tables[] = bkp_*

; Tables to dump.
; If specified, all other tables will be ignored.
; If not specified, all tables will be dumped except the ignored ones.
;dump_tables[] = queries

; Output folder where to write the dumps. Disk space will be checked before dump.
; The default output is a _dumps_ directory in the current working directory (will be created if it doesn't exist).
; Examples: 
; - dumps/[mysql_host]/[yearmonthdate]/[table].sql.gz
; - dumps/[mysql_host]/[yearmonthdate]-[schema].sql.gz
;output_folder = 

; The way to dump the database. Options are:
; - single - Each table is dumped to a separate file.
; - full - All tables are dumped to a single file. Default.
;output_mode = full

; Compress output using Gzip. This runs locally, even if ssh is configured.
; Options are: true, false. Default is true.
;compress = true

; When a table is larger than this limit, the table will be exported separately.
; Default value is 500, values are in MB.
;table_dump_limit = 500;


[mysql]

; The host to connect to, default is localhost.
;mysql_host = 

; The port to connect to, default is 3306.
;mysql_port = 

; The MySQL user
;mysql_user = 

; The MySQL password.
;mysql_pass = 


[ssh]

; When ssh is enabled, the server connects through SSH and executes the MySQL binaries remotely.
; Default option is to run local binaries.
;ssh = false

; Host to connect to, must be completed if using SSH mode.
;ssh_host = 

; Port to use for SSH, if ignored the default port 22 is used.
;ssh_port = 22

; User to use for SSH, if ignored the current user is used.
;ssh_user = 
```
