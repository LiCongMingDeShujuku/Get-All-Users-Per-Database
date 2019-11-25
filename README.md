![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 获取每个数据库的所有用户
#### Get All Users Per Database
**发布-日期: 2018年03月23日 (评论)**

![#](images/get-all-users-per-database-a.png?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
通常，获取用户和数据库列表是个好主意。传统上来说一个简单的方法就是使用sp_helplogins，但是把它移植到一个表中会有点混乱。谁想要一次只做一个数据库呢？这是一个等价的SQL逻辑，可以帮助你获得所有数据库和用户。


## English
 
Often times it’s a good idea to get a list of the users and databases. Traditionally; an easy method for this is use sp_helplogins, but porting this to a table a bit of a mess, and who wants to do it one database at a time? Here’s an equivalent bit of SQL logic that helps get you all the databases and users assigned.


---
## Logic
```SQL
use master;
set nocount on
 
if object_id('tempdb..#users_per_database') is not null
drop table  #users_per_database
create table    #users_per_database ([database] varchar(255), [name] varchar(255), [desc] varchar(255), [role] varchar(255))
 
declare @gather_users   varchar(max)
set @gather_users   = ''
select  @gather_users   = @gather_users +
'use [' + [name] + '];' + char(10) +
'select 
    db_name()
,   sdp.[name]
,   sdp.type_desc
,   user_name(sdrm.role_principal_id)
from 
    [' + [name] + '].sys.database_principals sdp left outer join 
    [' + [name] + '].sys.database_role_members sdrm 
    on sdp.principal_id = sdrm.member_principal_id 
where 
    sdp.sid not in (0x01,0x00) 
    and sdp.sid is not null 
    and sdp.type not in (''c'')
order by 
    name' + char(10)
from sys.databases 
where [name] not in ('tempdb') and [state_desc] = 'online'
insert into #users_per_database
exec (@gather_users)
 
select
    [database]
,   [name]
from
    #users_per_database
where
    [desc] not in
    (   'DATABASE_ROLE'
    ,   'SQL_USER'
    ,   'WINDOWS_GROUP'
    )
    and [name] not in
    (   'dbo'
    ,   'NT AUTHORITY\SYSTEM'
    ,   'NT AUTHORITY\LOCAL SERVICE'
    ,   'NT AUTHORITY\NETWORK SERVICE'
    )
    and [name] not like '%$%'


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

