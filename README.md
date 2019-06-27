# Renaming Microsoft SQL Server database along with objects name

1. Introduction:
This document explains Manual renaming process for Database installed on MS SQLServer.

2. Steps:
Following steps needs to be performed by the MS SQLServer administrator.

2.1. Take backup of the database you want to replicate.
For Example: 'dev'

2.2. Restore it with the same name in the new environment.

2.3. Login with ‘SA’ or Admin Credential in MS SQL management studio.

2.4. Execute following command.
sp_renamedb 'old_db_name', 'new_db_name'

Note: Rename ‘old_db_name’ with your existing database name and
‘new_db_name’ with the intended database name.
Example: sp_renamedb 'dev', 'test'

3. Create new login with same name as 'test'

4. Create new schema and user in database with same name as 'test'

5. Map new user to the new database, using below script.
use test
go
exec sp_change_users_login 'update_one', 'test', 'test'
go

6. Provide the permissions to user.
use master
go
grant view server state to test
go

Use test
go
grant create table to test
go

Use test
go
grant create procedure to test
go

Use test
go
grant create view to test
go

Use test
go
grant create function to test
go

7. Run the below command in query window.

select 'ALTER SCHEMA test TRANSFER dev.' + name + ';'
from sys.sysobjects where xtype in ('U','FN','V','P','TF')

Sample output of above script:
ALTER SCHEMA test TRANSFER dev.emp;

8. Execute all the statements generated in above step for TABLES, FUNCTIONS,
PROCEDURES, VIEWS with new user login. Make sure that there are no
errors reported in any of these scripts. 

9. Generate scripts for all Views, Stored procedures and Functions with
the help of 'Generate and Publish Script' wizard.
 
10. Replace 'dev' with 'test' in all these objects files
created in above step and Execute all these modified objects files with
'test' login to replace old objects.
