---
layout: post
title: PostgreSQL 
comments: true
permalink: "PostgreSQL"
---
## Database
`list databases`
psql -h localhost -U odoo -p 54321 --list

`truncate database`
sudo dropdb -h localhost -U odoo testpg


## Meta data
``` sql
-- \d+ <table_name> or the Information Schema 

SELECT *
FROM information_schema.columns
WHERE table_schema = 'your_schema'
 AND table_name   = 'your_table'
```

## Backup & Restore

``` bash
#dump table data only to SQL
pg_dump -h tsda -U odoo -a -t product_product -t product_template -t res_company -f ~/Documents/ecom/tsd_pg_1.sql pgtsd

pg_dump -h tsda -U odoo -a -t delivery_carrier -t delivery_grid -t delivery_grid_line -t fetchmail_server -t ir_mail_server -t website_menu -f ~/Documents/ecom/tsd_pg.sql pgtsd

#psql restores from text format
psql -h localhost -U odoo -d testpg -f ./tsd_pg_fetchmail.sql

#pg_restore is a utility for restoring a PostgreSQL database from an archive created by pg_dump

#create a custom file format at the time of backup of database using pg_dump
pg_dump -Fc testpg > testpg.gz

#And you can restore using the pg_restore command as below
createdb new-testpg --eocoding=UNICODE
pg_restore -d new-testpg testpg.gz
```
[openerp backup and restore](http://openerpbook.com/backup/01/index.html)