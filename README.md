> **Note** — The folder `linguist-samples/` contains tiny real files so GitHub can correctly display all languages used in this repo.  
> The actual content and examples remain in this README.

![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Sensible SQL Public Role Permissions
**Post Date: January 5, 2018**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process


<p>Creating a temp table to hold the Public Role permissions…
Here's some quick sql logic to help make more sense of the public role permissions. This might be helpful when trying to figure out what revokes you might need to write in order to better secure your database environment. Using a small temporary table (populated with public role permissions), I'm able to write a much more simplified query to find the permissions I'm looking for.

Get a basic set of Public Role permissions.
Get a quick report to add to your query-to-email using SMTP (if this is something you already have setup).
Finally; a couple short queries to get the dirt on the Public Role, and the extended stored procedures or some the system objects.</p>  
 
    
## SQL-Logic
```SQL
use master;
set nocount on
 
-- build temp table to hold public role permissions
if object_id('tempdb..#role_permissions') is not null
drop table      [#role_permissions]
create table    [#role_permissions]
(
    [role]      varchar(255)
,   [permissions]   varchar(255)
,   [schema]    varchar(255)
,   [object]    varchar(255)
)
insert into [#role_permissions]
select
    'role'      = upper(sdprin.name)
,   'permission'    = sdperm.permission_name
,   'schema'    = object_schema_name(so.object_id)
,   'object'    = so.name
from
    sys.all_objects so
    inner join sys.database_permissions sdperm on so.object_id          = sdperm.major_id
    inner join sys.database_principals  sdprin on sdprin.principal_id   = sdperm.grantee_principal_id
 
select 'number of role permissions' = count(*) from [#role_permissions]
select  top 20 * from [#role_permissions]
 
 
-- get existing permissions for PUBLIC role.
select
    [role]
,   [permissions]
,   'object' = [schema] + '.' + [object]
from
     [#role_permissions]
where
    [role] = 'public'
order by
    [object] asc
 
-- looking at it a more sensible way when reading it you can do this...
select
    'Quick Permissions Report'      = ' The role  ' + [role] + '  has  ' + [permissions] + '  permissions on object:  [' + [schema] + '].[' + [object] + ']  under the  [' + upper(db_name()) + ']  database.'
from
    [#role_permissions]
where
    [role] = 'public'
order by
    [object] asc
 
-- couple short queries to get public role permissions to xp's and some system objects
select [role], [permissions], 'object' = [schema] + '.' + [object] from [#role_permissions] where [object] like 'xp%'
select [role], [permissions], 'object' = [schema] + '.' + [object] from [#role_permissions] where [object] like 'sys%'
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)


## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")
