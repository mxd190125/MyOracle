## 项目简介

作为网络社交多媒体信息传播工具之一——微博，经过多年的积淀，已成为人们日常生活不可或缺的一部分。每天，全球通过微博发送、转发、分享信息的记录破亿。信息爆炸的大数据时代，微博功不可没。同时，随着生活水平的不断提高，人们对精神满足提出了更高的要求，为适应这种精神层面需求，微博的更新换代迫在眉睫。应用户委托，我们着手开发一款全新的、基于ssm的仿微博系统，它将在继承传统微博功能特性的基础上，给予用户全新体验，提供更舒适、更便捷的人性化服务。

## 功能简介

### 任务1：表，表空间设计合理，数据合理

#### 1、新键一个PDB：weibo

```sql
CREATE PLUGGABLE DATABASE weibo ADMIN USER admin IDENTIFIED BY 123 FILE_NAME_CONVERT=('home/oracle/app/oracle/oradata/orcl/pdbseed','home/oracle/app/oracle/oradata/orcl/weobo'); 
```

#### 2、划分表空间，划分出3个表空间：

1、weibo_1.dbf 大小500M 每次100M 无限大小 （用于存放大量数据：4W）

​	2、weibo_2.dbf 大小 500M 每次 100M 无限大小 （用于存放大量数据4W）

​	3、weibo_3.dbf 大小 100M 每次 50M 无限大小  （用于存放少量数据 1W）

```sql
1.create TABLESPACE usertablepspace DATAFILE   
2.'/home/oracle/app/oracle/oradata/orcl/weobo/weibo_1.dbf' SIZE 500M AUTOEXTEND ON NEXT 100M MAXSIZE UNLIMITED,   
3.'/home/oracle/app/oracle/oradata/orcl/weobo/weibo_2.dbf' SIZE 500M AUTOEXTEND ON NEXT 100M MAXSIZE UNLIMITED,  
4.'/home/oracle/app/oracle/oradata/orcl/weobo/weibo_3.dbf' SIZE 100M AUTOEXTEND ON NEXT 50M MAXSIZE UNLIMITED   
5.EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;  
6.select tablespace_name status,contents,logging from dba_tablespaces;  
```

#### 结果

![avator](picture\1.png)

![avator](picture\2.png)

#### 3、数据库设计

表汇总

管理员表、收藏表、评论表、喜欢表、位置表、mention表、关系表、回复表、用户表、微博表

| 表名            | 功能说明         |
| --------------- | ---------------- |
| anmin           | 管理员表         |
| collect         | 用于存储地层参数 |
| comment         | 收藏表           |
| likes           | 喜欢表           |
| location        | 位置表           |
| mention mention | mention表        |
| relation        | 关系表           |
| reply           | 回复表           |
| user            | 用户表           |
| weibo           | 微博表           |

#### 表A



| 表名     | admin                                                        |          |              |          |          |      |
| -------- | ------------------------------------------------------------ | -------- | ------------ | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段 | 字段类型     | 是否主键 | 是否外键 | 备注 |
| 1        | 管理员编号                                                   | admin_id | Int          | Y        |          |      |
| 2        | 用户名                                                       | username | password(50) |          |          |      |
| 3        | 密码                                                         | password | varchar(50)  |          |          |      |
| 建表语句 | **1.** ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."ADMIN"  2.  (  "ADMIN_ID" NUMBER(*,0) NOT NULL ENABLE,  3.   "COLUMN1" VARCHAR2(50 BYTE),  4.   "PASSWORD" VARCHAR2(50 BYTE) 5.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  6. NOCOMPRESS LOGGING 7.  STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 8.  PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 9.  BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 10.  TABLESPACE "SYSTEM" ; |          |              |          |          |      |

 

#### 表B

| 表名     | collect                                                      |              |          |          |          |      |
| -------- | ------------------------------------------------------------ | ------------ | -------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段     | 字段类型 | 是否主键 | 是否外键 | 备注 |
| 1        | 收藏ID                                                       | collect_id   | Int      | Y        |          | 非空 |
| 2        | 微博编号                                                     | weibo_id     | Int      |          | Y        |      |
| 3        | 收藏时间                                                     | collect_time | datatime |          |          |      |
| 建表语句 | 1. CREATE TABLESYSTEM"."COLLECT"  2.  ( "COLLECT_ID" NUMBER(*,0) NOT NULL ENABLE,  3. "WEIBO_ID" NUMBER(*,0),  4. "COLLECT_TIME" ***\*DATE\**** 5.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  6. NOCOMPRESS LOGGING 7. STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 8. PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 9. BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 10. TABLESPACE "SYSTEM" ; |              |          |          |          |      |

#### 表C

| 表名     | comment                                                      |                 |             |          |          |      |
| -------- | ------------------------------------------------------------ | --------------- | ----------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段        | 字段类型    | 是否主键 | 是否外键 | 备注 |
| 1        | 评论表id                                                     | comment_id      | Int         | Y        |          |      |
| 2        | 用户id                                                       | uer_id          | Int         |          | Y        |      |
| 3        | 微博id                                                       | weibo_id        | Int         |          |          |      |
| 4        | 评论时间                                                     | comment_id      | datatime    |          |          |      |
| 5        | 评论内容                                                     | comment_content | Varchar(50) |          |          |      |
| 建表语句 | 1. ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."COMMENT_WEIBO"  2.  ( "COMMENT_ID" NUMBER NOT NULL ENABLE,  3. "USER_ID" NUMBER,  4. "COLUMN1" NUMBER,  5. "COMMENT_TIME" ***\*DATE\****,  6. "COLUMN2" VARCHAR2(50 BYTE) 7.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  8. NOCOMPRESS LOGGING 9. STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 10. PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 11. BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 12. TABLESPACE "SYSTEM" ; |                 |             |          |          |      |

#### 表D

| 表名     | likes                                                        |           |          |          |          |      |
| -------- | ------------------------------------------------------------ | --------- | -------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段  | 字段类型 | 是否主键 | 是否外键 | 备注 |
| 1        | 喜欢id                                                       | like_id   | Int      | Y        |          |      |
| 2        | 用户id                                                       | user_id   | Int      |          | Y        |      |
| 3        | 微博id                                                       | weibo_id  | Int      |          |          |      |
| 4        | 喜欢时间                                                     | like_time | datatime |          |          |      |
| 建表语句 | **1.** ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."LIKES"  2.  (  "LIKOES_ID" NUMBER NOT NULL ENABLE,  3.   "USER_ID" NUMBER,  4.   "WEIBO_ID" NUMBER,  5.   "LIKE_TIME" ***\*DATE\**** 6.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  7. NOCOMPRESS LOGGING 8.  STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 9.  PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 10.  BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 11.  TABLESPACE "SYSTEM" ; |           |          |          |          |      |

#### 表E

| 表名     | location                                                     |          |             |          |          |      |
| -------- | ------------------------------------------------------------ | -------- | ----------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段 | 字段类型    | 是否主键 | 是否外籍 | 备注 |
| 1        | 编号                                                         | id       | Int         | Y        |          |      |
| 2        | 名字                                                         | name     | Varchar(50) |          |          |      |
| 建表语句 | 1. ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."LOCATION"  2.  ( "ID" NUMBER NOT NULL ENABLE,  3. "NAME" VARCHAR2(50 BYTE) 4.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  5. NOCOMPRESS LOGGING 6. STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 7. PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 8. BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 9. TABLESPACE "SYSTEM" ; |          |             |          |          |      |

#### 表F

| 表名     | mention                                                      |              |          |          |          |      |
| -------- | ------------------------------------------------------------ | ------------ | -------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段     | 字段类型 | 是否主键 | 是否外键 | 备注 |
| 1        | 提到id                                                       | mention_id   | Int      | Y        |          |      |
| 2        | 用户id                                                       | user_id      | Int      |          | Y        |      |
| 3        | 报告id                                                       | reportCount  | Int      |          |          |      |
| 4        | 评论条数                                                     | commentCount | Int      |          |          |      |
| 5        | 回复条数                                                     | replayCount  | Int      |          |          |      |
| 6        | 喜欢条数                                                     | likeCount    | Int      |          |          |      |
| 7        | 粉丝数                                                       | fansCount    | Int      |          |          |      |
| 建表语句 | **1.** ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."MENTION"  2.  (  "MENTION_ID" NUMBER NOT NULL ENABLE,  3.   "COLUMN1" NUMBER,  4.   "REPORTCOUNT" NUMBER,  5.   "COMMENTCOUNT" NUMBER,  6.   "A" NUMBER,  7.   "LIKECOUNT" NUMBER,  8.   "FANSCOUNT" VARCHAR2(20 BYTE) 9.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  10. NOCOMPRESS LOGGING 11.  STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 12.  PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 13.  BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 14.  TABLESPACE "SYSTEM" ; |              |          |          |          |      |

#### 表G

| 表名     | relation                                                     |             |          |          |          |      |
| -------- | ------------------------------------------------------------ | ----------- | -------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段    | 字段类型 | 是否主键 | 是否外键 | 备注 |
| 1        | 关系编号                                                     | relation_id | Int      | Y        |          |      |
| 2        | 用户id                                                       | user_id     | Int      |          | Y        |      |
| 3        | 关注编号                                                     | followid    | Int      |          |          |      |
| 4        | 状态                                                         | state       | Int      |          |          |      |
| 建表语句 | 1. ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."RELATION"  2.  ( "RELATION_ID" NUMBER NOT NULL ENABLE,  3. "USER_ID" NUMBER,  4. "FOLLOWID" NUMBER,  5. "STATE" NUMBER 6.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  7. NOCOMPRESS LOGGING 8. STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 9. PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 10. BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 11. TABLESPACE "SYSTEM" ; |             |          |          |          |      |

#### 表H

| 表名     | reply                                                        |               |               |          |          |      |
| -------- | ------------------------------------------------------------ | ------------- | ------------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段      | 字段类型      | 是否主键 | 是否外键 | 备注 |
| 1        | 回复id                                                       | replay_id     | Int           | Y        |          |      |
| 2        | 回复id                                                       | comment_id    | Int           |          | Y        |      |
| 3        | 表单编号                                                     | from_id       | Int           |          |          |      |
| 4        | 评论对象id                                                   | to_id         | Int           |          |          |      |
| 5        | 评论                                                         | reply_content | Varchar(1000) |          |          |      |
| 6        | 时间                                                         | time          | datatime      |          |          |      |
| 建表语句 | **1.** ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."REPLAY"  2.  (  "RELAY_ID" NUMBER NOT NULL ENABLE,  3.   "COMMENT_ID" NUMBER,  4.   "FROM_ID" NUMBER,  5.   "TO_ID" VARCHAR2(50 BYTE),  6.   "REPLAY_CONTENT" VARCHAR2(20 BYTE),  7.   "TIME" ***\*DATE\**** 8.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  9. NOCOMPRESS LOGGING 10.  STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 11.  PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 12.  BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 13.  TABLESPACE "SYSTEM" ; |               |               |          |          |      |

#### 表I

| 表名     | user                                                         |          |             |          |          |      |
| -------- | ------------------------------------------------------------ | -------- | ----------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段 | 字段类型    | 是否主键 | 是否外键 | 备注 |
| 1        | 用户表                                                       | user_id  | Int         | Y        |          |      |
| 2        | 用户名                                                       | username | Int         |          | Y        |      |
| 3        | 密码                                                         | password | Varchar(50) |          |          |      |
| 4        | 昵称                                                         | nickname | Varchar(50) |          |          |      |
| 5        | 头像                                                         | face     | Varchar(50) |          |          |      |
| 6        | 性别                                                         | sex      | Int         |          |          |      |
| 8        | 生日                                                         | bir      | data        |          |          |      |
| 9        | 省份                                                         | province | Int         |          |          |      |
| 10       | 城市                                                         | city     | Int         |          |          |      |
| 建表语句 | 1. ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."USER_WIEBO"  2.  ( "USER_ID" NUMBER NOT NULL ENABLE,  3. "USERNAME" VARCHAR2(50 BYTE),  4. "PASSWORD" VARCHAR2(50 BYTE),  5. "NICKNAME" VARCHAR2(50 BYTE),  6. "FACE" VARCHAR2(50 BYTE),  7. "SEX" VARCHAR2(50 BYTE),  8. "BIR" ***\*DATE\****,  9. "PROVINCE" VARCHAR2(50 BYTE),  10. "CITY" VARCHAR2(50 BYTE) 11.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  12. NOCOMPRESS LOGGING 13. STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 14. PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 15. BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 16. TABLESPACE "SYSTEM" ; |          |             |          |          |      |

 

#### 表J

| 表名     | weibo                                                        |           |             |          |          |      |
| -------- | ------------------------------------------------------------ | --------- | ----------- | -------- | -------- | ---- |
| 字段编号 | 物理字段                                                     | 逻辑字段  | 字段类型    | 是否主键 | 是否外键 | 备注 |
| 1        | 微博id                                                       | user_id   | Int         | Y        |          |      |
| 2        | 用户id                                                       | username  | Int         |          | Y        |      |
| 3        | 发送时间                                                     | password  | Varchar(50) |          |          |      |
| 4        | 图片                                                         | nickname  | Varchar(50) |          |          |      |
| 5        | 图片                                                         | face      | Varchar(50) |          |          |      |
| 6        | 图片                                                         | sex       | Int         |          |          |      |
| 8        | 图片                                                         | bir       | data        |          |          |      |
| 9        | 原始微博                                                     | original  | Int         |          |          |      |
| 10       | 报告ID                                                       | repost_id | Int         |          |          |      |
| 建表语句 | 1. ***\*CREATE\**** ***\*TABLE\**** "SYSTEM"."USER_WIEBO"  2.  ( "USER_ID" NUMBER NOT NULL ENABLE,  3. "USERNAME" VARCHAR2(50 BYTE),  4. "PASSWORD" VARCHAR2(50 BYTE),  5. "NICKNAME" VARCHAR2(50 BYTE),  6. "FACE" VARCHAR2(50 BYTE),  7. "SEX" VARCHAR2(50 BYTE),  8. "BIR" ***\*DATE\****,  9. "PROVINCE" VARCHAR2(50 BYTE),  10. "CITY" VARCHAR2(50 BYTE) 11.  ) PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255  12. NOCOMPRESS LOGGING 13. STORAGE(INITIAL 65536 ***\*NEXT\**** 1048576 MINEXTENTS 1 MAXEXTENTS 2147483645 14. PCTINCREASE 0 FREELISTS 1 FREELIST GROUPS 1 15. BUFFER_POOL ***\*DEFAULT\**** FLASH_CACHE ***\*DEFAULT\**** CELL_FLASH_CACHE ***\*DEFAULT\****) 16. TABLESPACE "SYSTEM" ; |           |             |          |          |      |

数据写入

写入admin数据：1W条：

```sql
1.declare  
2.  v_order_id number(10);  
3.  v_name varchar2(100);  
4.begin  
5.    delete from ADMIN;  
6.    for i in 1..10000  
7.    loop  
8.        v_order_id:=i;  
9.        v_name := 'zhang' || i;  
10.        insert /*+append*/ into ADMIN (ADMIN.ADMIN_ID,ADMIN.USERNAME,ADMIN.PASSWORD) values (v_order_id,v_name,'123456');  
11.    end loop;  
end;  
```

写入user_weibo数据：2W条：

 

```sql
1.declare  
2.  v_id number(10);  
3.  v_name varchar2(100);  
4.  v_password varchar2(50);  
5.  v_nicknane varchar(100);  
6.  v_face varchar(100);  
7.  v_sex varchar(2);  
8.  v_bir varchar(100);  
9.  v_province varchar(100);  
10.  v_city varchar(100);  
11.begin  
12.    delete from ADMIN;  
13.    for i in 1..10000  
14.    loop  
15.        v_id:=i;  
16.        v_name := 'zhang' || i;  
17.        v_nicknane:= 'zhang' || i;  
18.        v_face:='asdfafasdff';  
19.        v_sex:=1;  
20.        v_bir:='1997.11.1';  
21.        v_province:='sichuan';  
22.        v_city :='chengDu';  
23.        insert /*+append*/ into user_weibo (user_weibo.USER_ID,user_weibo.username,user_weibo.password,  
24.        user_weibo.nickname,user_weibo.face,user_weibo.sex,user_weibo.province,user_weibo.CITY)   
25.        values (v_id,v_name,'123456',v_nicknane,v_face,v_sex,v_province,v_city);  
26.    end loop;  
end;  
```

写入微博数据：4W条：

```sql
1.declare  
2.  v_id number(10);  
3.  v_user_id varchar2(100);  
4.  v_content varchar2(50);  
5.begin  
6.    delete from ADMIN;  
7.    for i in 1..40000  
8.    loop  
9.        v_id:=i;  
10.        v_user_id := 1;  
11.        v_content:= 'testtetsetesteststs';  
12.  
13.        insert /*+append*/ into weibo (weibo.WEIBO_ID,weibo.USERNAME,weibo.CONTENT)   
14.        values (v_id,v_user_id,v_content);  
15.    end loop;  
end;  
```

#### 任务2：权限及用户分配方案设计正确

1、创建1管理员个角色

​	创建一个管理员角色并赋予RESOURCE,CONNECT角色。

```sql
1.CREATE ROLE con_res;  
grant connect,resource to con_res;  
```

2、创建4个用户角色

​	admin：管理员用户并将所有权限赋予它

```sql
1.GRANT "DBA" TO "ADMIN" WITH ADMIN OPTION;  
2.GRANT "C##CON_RES" TO "ADMIN" WITH ADMIN OPTION;  
3.GRANT "DV_MONITOR" TO "ADMIN" WITH ADMIN OPTION;  
4.GRANT "CTXAPP" TO "ADMIN" WITH ADMIN OPTION;  
5.GRANT "DV_AUDIT_CLEANUP" TO "ADMIN" WITH ADMIN OPTION;  
6.GRANT "SPATIAL_CSW_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
7.GRANT "APEX_GRANTS_FOR_NEW_USERS_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
8.GRANT "WFS_USR_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
9.GRANT "EM_EXPRESS_ALL" TO "ADMIN" WITH ADMIN OPTION;  
10.GRANT "WM_ADMIN_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
11.GRANT "OLAP_USER" TO "ADMIN" WITH ADMIN OPTION;  
12.GRANT "OLAP_XS_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
13.GRANT "DV_SECANALYST" TO "ADMIN" WITH ADMIN OPTION;  
14.GRANT "CSW_USR_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
15.GRANT "XS_CACHE_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
16.GRANT "GDS_CATALOG_SELECT" TO "ADMIN" WITH ADMIN OPTION;  
17.GRANT "SCHEDULER_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
18.GRANT "PROVISIONER" TO "ADMIN" WITH ADMIN OPTION;  
19.GRANT "AUDIT_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
20.GRANT "XDB_WEBSERVICES_OVER_HTTP" TO "ADMIN" WITH ADMIN OPTION;  
21.GRANT "DV_REALM_RESOURCE" TO "ADMIN" WITH ADMIN OPTION;  
22.GRANT "AQ_ADMINISTRATOR_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
23.GRANT "DELETE_CATALOG_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
24.GRANT "CON_RES" TO "ADMIN" WITH ADMIN OPTION;  
25.GRANT "XDB_WEBSERVICES" TO "ADMIN" WITH ADMIN OPTION;  
26.GRANT "DV_PUBLIC" TO "ADMIN" WITH ADMIN OPTION;  
27.GRANT "LBAC_DBA" TO "ADMIN" WITH ADMIN OPTION;  
28.GRANT "OPTIMIZER_PROCESSING_RATE" TO "ADMIN" WITH ADMIN OPTION;  
29.GRANT "RECOVERY_CATALOG_USER" TO "ADMIN" WITH ADMIN OPTION;  
30.GRANT "DV_DATAPUMP_NETWORK_LINK" TO "ADMIN" WITH ADMIN OPTION;  
31.GRANT "GSMUSER_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
32.GRANT "GATHER_SYSTEM_STATISTICS" TO "ADMIN" WITH ADMIN OPTION;  
33.GRANT "LOGSTDBY_ADMINISTRATOR" TO "ADMIN" WITH ADMIN OPTION;  
34.GRANT "GSM_POOLADMIN_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
35.GRANT "DV_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
36.GRANT "HS_ADMIN_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
37.GRANT "XS_SESSION_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
38.GRANT "DV_GOLDENGATE_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
39.GRANT "IMP_FULL_DATABASE" TO "ADMIN" WITH ADMIN OPTION;  
40.GRANT "DV_XSTREAM_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
41.GRANT "DV_PATCH_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
42.GRANT "DATAPUMP_EXP_FULL_DATABASE" TO "ADMIN" WITH ADMIN OPTION;  
43.GRANT "EJBCLIENT" TO "ADMIN" WITH ADMIN OPTION;  
44.GRANT "HS_ADMIN_EXECUTE_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
45.GRANT "JMXSERVER" TO "ADMIN" WITH ADMIN OPTION;  
46.GRANT "OLAP_DBA" TO "ADMIN" WITH ADMIN OPTION;  
47.GRANT "ADM_PARALLEL_EXECUTE_TASK" TO "ADMIN" WITH ADMIN OPTION;  
48.GRANT "JAVAIDPRIV" TO "ADMIN" WITH ADMIN OPTION;  
49.GRANT "SELECT_CATALOG_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
50.GRANT "JAVADEBUGPRIV" TO "ADMIN" WITH ADMIN OPTION;  
51.GRANT "CONNECT" TO "ADMIN" WITH ADMIN OPTION;  
52.GRANT "DATAPUMP_IMP_FULL_DATABASE" TO "ADMIN" WITH ADMIN OPTION;  
53.GRANT "OEM_MONITOR" TO "ADMIN" WITH ADMIN OPTION;  
54.GRANT "APEX_ADMINISTRATOR_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
55.GRANT "GSMADMIN_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
56.GRANT "AQ_USER_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
57.GRANT "JAVAUSERPRIV" TO "ADMIN" WITH ADMIN OPTION;  
58.GRANT "XDB_SET_INVOKER" TO "ADMIN" WITH ADMIN OPTION;  
59.GRANT "RECOVERY_CATALOG_OWNER" TO "ADMIN" WITH ADMIN OPTION;  
60.GRANT "JAVA_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
61.GRANT "DBFS_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
62.GRANT "PDB_DBA" TO "ADMIN" WITH ADMIN OPTION;  
63.GRANT "DV_GOLDENGATE_REDO_ACCESS" TO "ADMIN" WITH ADMIN OPTION;  
64.GRANT "CDB_DBA" TO "ADMIN" WITH ADMIN OPTION;  
65.GRANT "JAVASYSPRIV" TO "ADMIN" WITH ADMIN OPTION;  
66.GRANT "HS_ADMIN_SELECT_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
67.GRANT "AUDIT_VIEWER" TO "ADMIN" WITH ADMIN OPTION;  
68.GRANT "RESOURCE" TO "ADMIN" WITH ADMIN OPTION;  
69.GRANT "DV_OWNER" TO "ADMIN" WITH ADMIN OPTION;  
70.GRANT "XDB_WEBSERVICES_WITH_PUBLIC" TO "ADMIN" WITH ADMIN OPTION;  
71.GRANT "EXECUTE_CATALOG_ROLE" TO "ADMIN" WITH ADMIN OPTION;  
72.GRANT "DV_ACCTMGR" TO "ADMIN" WITH ADMIN OPTION;  
73.GRANT "DV_REALM_OWNER" TO "ADMIN" WITH ADMIN OPTION;  
74.GRANT "EXP_FULL_DATABASE" TO "ADMIN" WITH ADMIN OPTION;  
75.GRANT "DV_STREAMS_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
76.GRANT "JAVA_DEPLOY" TO "ADMIN" WITH ADMIN OPTION;  
77.GRANT "SPATIAL_WFS_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
78.GRANT "XS_NAMESPACE_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
79.GRANT "DEV" TO "ADMIN" WITH ADMIN OPTION;  
80.GRANT "XS_RESOURCE" TO "ADMIN" WITH ADMIN OPTION;  
81.GRANT "ORDADMIN" TO "ADMIN" WITH ADMIN OPTION;  
82.GRANT "AUTHENTICATEDUSER" TO "ADMIN" WITH ADMIN OPTION;  
83.GRANT "CAPTURE_ADMIN" TO "ADMIN" WITH ADMIN OPTION;  
84.GRANT "OEM_ADVISOR" TO "ADMIN" WITH ADMIN OPTION;  
85.GRANT "XDBADMIN" TO "ADMIN" WITH ADMIN OPTION;  
GRANT "EM_EXPRESS_BASIC" TO "ADMIN" WITH ADMIN OPTION;  
```

 

2、zhangdev：开发用户1，只赋予weibo,user_weibo表的所有权限

```sql
1.CREATE USER zhangdev IDENTIFIED BY "123"  
2.grant all on weibo to zhangdev;  
grant all on user_weibo to zhangdev; 
```

3、chengdev：开发用户2，赋予开发角色1的所有权限，同时开放所有的：

​		index, select,debug,update,delete

​	对象给这个用户

```sql
1.CREATE USER chengdev IDENTIFIED BY "123"    
2.grant all on user_weibo  to chengdev;  
3.grant all on weibo  to chengdev;  
4.grant  index,select,debug,update,delete  on admin to zhangdev;  
5.grant  index,select,debug,update,delete  on collect to zhangdev;  
6.grant  index,select,debug,update,delete  on comment_weibo to zhangdev;  
7.grant  index,select,debug,update,delete  on likes to zhangdev;  
8.grant  index,select,debug,update,delete  on location to zhangdev;  
9.grant  index,select,debug,update,delete  on mention  to zhangdev;  
10.grant  index,select,debug,update,delete  on relation to zhangdev;  
grant  index,select,debug,update,delete  on replay  to zhangdev;  
```

3、wangtest：测试用户

```sql
1.CREATE USER wangtest IDENTIFIED BY "123"    
2.grant  select,debug on admin to wangtest;  
3.grant  select,debug,delete  on collect to wangtest;  
4.grant  select,debug  on comment_weibo to wangtest;  
5.grant  select,debug on likes to wangtest;  
6.grant  select,debug  on location to wangtest;  
7.grant  select,debug on mention  to wangtest;  
8.grant  select,debug on relation to wangtest;  
9.grant  select,debug on replay  to wangtest;  
10.grant  select,debug on user_weibo  to wangtest;  
grant  select,debug on seibo  to wangtest;  
```

####  任务PL/SQL设计：存储过程和函数设计正确

1、函数1

```sql
1.create or replace FUNCTION Get_weibo(v_id  NUMBER) RETURN NUMBER  
2.AS  
3.    N NUMBER;  
4.    BEGIN  
5.        SELECT COUNT(*) INTO N from weibo where weibo.weibo_id=v_id;  
6.        RETURN N;  
    END; 
```

2、存储过程1

```sql
1.create or replace PROCEDURE GET_user(v_id NUMBER, p_cur out sys_refcursor)   
2.AS  
3.BEGIN  
4.   open p_cur for SELECT *FROM user_weibo where user_weibo.user_id=v_id;  
5.END;  
```

#### 任务4备份方案：备份方案设计正确

1、数据逻辑备份

执行：

```sql
expdp study/123@pdborcl directory=expdir dumpfile=study.dmp 
```

结果：

```sql
1.Export: Release 12.1.0.2.0 - Production on 星期一 11月 25 10:42:17 2019  
2.  
3.Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.  
4.  
5.Connected to: Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production  
6.With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options  
7.ORA-39001: 参数值无效  
8.ORA-39000: 转储文件说明错误  
9.ORA-31641: 无法创建转储文件 "/home/oracle/expdir/study.dmp"  
10.ORA-27038: 所创建的文件已存在  
11.Additional information: 1  
12.  
13.  
14.[oracle@oracle-pc Desktop]$ expdp study/123@pdborcl directory=expdir dumpfile=study.dmp  
15.  
16.Export: Release 12.1.0.2.0 - Production on 星期一 11月 25 10:43:40 2019  
17.  
18.Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.  
19.  
20.Connected to: Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production  
21.With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options  
22.启动 "STUDY"."SYS_EXPORT_SCHEMA_01":  study/********@pdborcl directory=expdir dumpfile=study.dmp   
23.正在使用 BLOCKS 方法进行估计...  
24.处理对象类型 SCHEMA_EXPORT/TABLE/TABLE_DATA  
25.使用 BLOCKS 方法的总估计: 33.31 MB  
26.处理对象类型 SCHEMA_EXPORT/PRE_SCHEMA/PROCACT_SCHEMA  
27.处理对象类型 SCHEMA_EXPORT/SEQUENCE/SEQUENCE  
28.处理对象类型 SCHEMA_EXPORT/TABLE/TABLE  
29.处理对象类型 SCHEMA_EXPORT/TABLE/COMMENT  
30.处理对象类型 SCHEMA_EXPORT/PACKAGE/PACKAGE_SPEC  
31.处理对象类型 SCHEMA_EXPORT/PROCEDURE/PROCEDURE  
32.处理对象类型 SCHEMA_EXPORT/PACKAGE/COMPILE_PACKAGE/PACKAGE_SPEC/ALTER_PACKAGE_SPEC  
33.处理对象类型 SCHEMA_EXPORT/PROCEDURE/ALTER_PROCEDURE  
34.处理对象类型 SCHEMA_EXPORT/VIEW/VIEW  
35.处理对象类型 SCHEMA_EXPORT/PACKAGE/PACKAGE_BODY  
36.处理对象类型 SCHEMA_EXPORT/TABLE/INDEX/INDEX  
37.处理对象类型 SCHEMA_EXPORT/TABLE/CONSTRAINT/CONSTRAINT  
38.处理对象类型 SCHEMA_EXPORT/TABLE/INDEX/STATISTICS/INDEX_STATISTICS  
39.处理对象类型 SCHEMA_EXPORT/TABLE/CONSTRAINT/REF_CONSTRAINT  
40.处理对象类型 SCHEMA_EXPORT/TABLE/TRIGGER  
41.处理对象类型 SCHEMA_EXPORT/TABLE/STATISTICS/TABLE_STATISTICS  
42.处理对象类型 SCHEMA_EXPORT/STATISTICS/MARKER  
43.处理对象类型 SCHEMA_EXPORT/POST_SCHEMA/PROCOBJ  
44.. . 导出了 "STUDY"."ORDERS":"PARTITION_BEFORE_2016"    268.4 KB    5000 行  
45.. . 导出了 "STUDY"."ORDERS":"PARTITION_BEFORE_2017"    268.5 KB    5000 行  
46.. . 导出了 "STUDY"."EMPLOYEES"                         70.67 KB       7 行  
47.. . 导出了 "STUDY"."DEPARTMENTS"                       5.593 KB       3 行  
48.. . 导出了 "STUDY"."PRODUCTS"                          6.296 KB      15 行  
49.. . 导出了 "STUDY"."ORDERS":"PARTITION_BEFORE_2018"        0 KB       0 行  
50.. . 导出了 "STUDY"."ORDER_DETAILS":"PARTITION_BEFORE_2016"  425.7 KB   15000 行  
51.. . 导出了 "STUDY"."ORDER_DETAILS":"PARTITION_BEFORE_2017"  426.1 KB   15000 行  
52.. . 导出了 "STUDY"."ORDER_DETAILS":"PARTITION_BEFORE_2018"      0 KB       0 行  
53.已成功加载/卸载了主表 "STUDY"."SYS_EXPORT_SCHEMA_01"   
54.******************************************************************************  
55.STUDY.SYS_EXPORT_SCHEMA_01 的转储文件集为:  
56.  /home/oracle/expdir/study.dmp  
57.作业 "STUDY"."SYS_EXPORT_SCHEMA_01" 已于 星期一 11月 25 10:44:55 2019 elapsed 0 00:01:14 成功完成  

```

2、自动备份

执行脚本1，内容如下：

```bash
 1.#rman_level0.sh   
2.#!/bin/sh  
3.  
4.export NLS_LANG='SIMPLIFIED CHINESE_CHINA.AL32UTF8'  
5.export ORACLE_HOME=/home/oracle/app/oracle/product/12.1.0/dbhome_1    
6.export ORACLE_SID=orcl    
7.export PATH=$ORACLE_HOME/bin:$PATH    
8.  
9.  
10.rman target / nocatalog msglog=/home/oracle/rman_backup/lv0_`date +%Y%m%d-%H%M%S`_L0.log << EOF  
11.run{  
12.configure retention policy to redundancy 1;  
13.configure controlfile autobackup on;  
14.configure controlfile autobackup format for device type disk to '/home/oracle/rman_backup/%F';  
15.configure default device type to disk;  
16.crosscheck backup;  
17.crosscheck archivelog all;  
18.allocate channel c1 device type disk;  
19.backup as compressed backupset incremental level 0 database format '/home/oracle/rman_backup/dblv0_%d_%T_%U.bak'  
20.   plus archivelog format '/home/oracle/rman_backup/arclv0_%d_%T_%U.bak';  
21.report obsolete;  
22.delete noprompt obsolete;  
23.delete noprompt expired backup;  
24.delete noprompt expired archivelog all;  
25.release channel c1;  
26.}  
27.EOF  
28.  
29.exit  
```

执行脚本2，内容如下：

 

```bash
1.#rman_level1.sh   
2.#!/bin/sh  
3.  
4.export NLS_LANG='SIMPLIFIED CHINESE_CHINA.AL32UTF8'  
5.export ORACLE_HOME=/home/oracle/app/oracle/product/12.1.0/dbhome_1    
6.export ORACLE_SID=orcl    
7.export PATH=$ORACLE_HOME/bin:$PATH    
8.  
9.  
10.rman target / nocatalog msglog=/home/oracle/rman_backup/lv1_`date +%Y%m%d-%H%M%S`_L0.log << EOF  
11.run{  
12.configure retention policy to redundancy 1;  
13.configure controlfile autobackup on;  
14.configure controlfile autobackup format for device type disk to '/home/oracle/rman_backup/%F';  
15.configure default device type to disk;  
16.crosscheck backup;  
17.crosscheck archivelog all;  
18.allocate channel c1 device type disk;  
19.backup as compressed backupset incremental level 1 database format '/home/oracle/rman_backup/dblv1_%d_%T_%U.bak'  
20.   plus archivelog format '/home/oracle/rman_backup/arclv1_%d_%T_%U.bak';  
21.report obsolete;  
22.delete noprompt obsolete;  
23.delete noprompt expired backup;  
24.delete noprompt expired archivelog all;  
25.release channel c1;  
26.}  
27.EOF  
28.  
29.exit  
```

#### 任务5容灾方案：DataGuard设计正确

1、基础配置

| Type           | Primary         | Standby         |
| -------------- | --------------- | --------------- |
| IP地址         | 192.168.255.130 | 192.168.255.131 |
| db_name        | orcl            | orcl            |
| sid            | orcl            | orcl            |
| di_unique_name | orcl            | stdorcl         |

2、在备库Standby中创建以下文件：

```bash
1.mkdir -p /home/oracle/app/oracle/diag/orcl  
2.mkdir -p /home/oracle/app/oracle/oradata/stdorcl/  
3.mkdir -p /home/oracle/app/oracle/oradata/stdorcl/pdborcl  
4.mkdir -p /home/oracle/arch  
5.mkdir -p /home/oracle/rman  
6.mkdir -p /home/oracle/app/oracle/oradata/stdorcl/pdbseed/  
7.mkdir -p /home/oracle/app/oracle/oradata/stdorcl/pdb/  
```



结果如图：

![avator](picture\3.png)

 

3、删除原有的数据库

```sql
1.$sqlplus / as sysdba  
2.shutdown immediate;  
3.startup mount exclusive restrict;  
4.drop database;  
```

4、将数据库启动到nomount状态

```sql
1.$sqlplus / as sysdba  
2.startup nomount  
```

主库操作

```sql
1.$sqlplus /  sysdba  
2.select group#,thread#,members,status from v$log;  
3.  
4.alter database add standby logfile  group 5 '/home/oracle/app/oracle/oradata/orcl/stdredo1.log' size 50m; 
5.alter database add standby logfile  group 6 '/home/oracle/app/oracle/oradata/orcl/stdredo2.log' size 50m; 
6.alter database add standby logfile  group 7 '/home/oracle/app/oracle/oradata/orcl/stdredo3.log' size 50m; 
7.alter database add standby logfile  group 8 '/home/oracle/app/oracle/oradata/orcl/stdredo4.log' size 50m; 
```

1、主库强制开启归档模式

```sql
1.ALTER DATABASE FORCE LOGGING;  
2.  
3.alter system set LOG_ARCHIVE_CONFIG='DG_CONFIG=(orcl,stdorcl)' scope=both sid='*';           
4.alter system set log_archive_dest_1='LOCATION=/home/oracle/arch VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=orcl' scope=spfile;  
5.alter system set LOG_ARCHIVE_DEST_2='SERVICE=stdorcl LGWR ASYNC  VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=stdorcl' scope=both sid='*';  
6.alter system set fal_client='orcl' scope=both sid='*';      
7.alter system set FAL_SERVER='stdorcl' scope=both sid='*';    
8.alter system set standby_file_management=AUTO scope=both sid='*';  
9.alter system set DB_FILE_NAME_CONVERT='/home/oracle/app/oracle/oradata/stdorcl/','/home/oracle/app/oracle/oradata/orcl/' scope=spfile sid='*';    
10.alter system set LOG_FILE_NAME_CONVERT='/home/oracle/app/oracle/oradata/stdorcl/','/home/oracle/app/oracle/oradata/orcl/' scope=spfile sid='*';  
11.alter system set log_archive_format='%t_%s_%r.arc' scope=spfile sid='*';  
12.alter system set remote_login_passwordfile='EXCLUSIVE' scope=spfile;  
13.alter system set PARALLEL_EXECUTION_MESSAGE_SIZE=8192 scope=spfile;  
```

编辑主库与备库

编辑文件:/home/oracle/app/oracle/product/12.1.0/dbhome_1/network/admin/tnsnames.ora

*(这里的IP地址是你自己)*

```sql
1.$gedit /home/oracle/app/oracle/product/12.1.0/dbhome_1/network/admin/tnsnames.ora  
2.  
3.ORCL =  
4.  (DESCRIPTION =  
5.    (ADDRESS_LIST =  
6.      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.255.130)(PORT = 1521))  
7.    )  
8.    (CONNECT_DATA =  
9.      (SERVER = DEDICATED)  
10.      (SERVICE_NAME = orcl)  
11.    )  
12.  )  
13.  
14.stdorcl =  
15.  (DESCRIPTION =  
16.    (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.255.131)(PORT = 1521))  
17.    (CONNECT_DATA =  
18.      (SERVER = DEDICATED)  
19.      (SID = orcl)  
20.    )  
21.  )  
```

2、回到主库，在主库上生成备份的参数文件

```sql
SQL>create pfile from spfile;  
```

3、将主库的参数文件，密码文件拷贝到备库

```sql
1.scp /home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/initorcl.ora 192.168.255.131:/home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/  
2.scp /home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/orapworcl 192.168.255.131:/home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/  
```

4、将主库复制到备库

```sql
1.run{   
2.allocate channel c1 type disk;  
3.allocate channel c2 type disk;  
4.allocate channel c3 type disk;  
5.allocate AUXILIARY channel c4 type disk;  
6.allocate AUXILIARY channel c5 type disk;  
7.allocate AUXILIARY channel c6 type disk;  
8.DUPLICATE TARGET DATABASE  
9.  FOR STANDBY  
10.  FROM ACTIVE DATABASE  
11.  DORECOVER  
12.  NOFILENAMECHECK;  
13.release channel c1;  
14.release channel c2;  
15.release channel c3;  
16.release channel c4;  
17.release channel c5;  
18.release channel c6;  
19.}  
```

备库操作

1、在备库上更改参数文件

```sql
$gedit /home/oracle/app/oracle/product/12.1.0/dbhome_1/dbs/initorcl.ora  
```

文件内容是：

```sql
1.orcl.__data_transfer_cache_size=0  
2.orcl.__db_cache_size=671088640  
3.orcl.__java_pool_size=16777216  
4.orcl.__large_pool_size=33554432  
5.orcl.__oracle_base='/home/oracle/app/oracle'#ORACLE_BASE set from environment  
6.orcl.__pga_aggregate_target=536870912  
7.orcl.__sga_target=1258291200  
8.orcl.__shared_io_pool_size=50331648  
9.orcl.__shared_pool_size=301989888  
10.orcl.__streams_pool_size=0  
11.*._allow_resetlogs_corruption=TRUE  
12.*._catalog_foreign_restore=FALSE  
13.*.audit_file_dest='/home/oracle/app/oracle/admin/orcl/adump'  
14.*.audit_trail='db'  
15.*.compatible='12.1.0.2.0'  
16.*.control_files='/home/oracle/app/oracle/oradata/orcl/control01.ctl','/home/oracle/app/oracle/fast_recovery_area/orcl/control02.ctl','/home/oracle/app/oracle/fast_recovery_area/orcl/control03.ctl'  
17.*.db_block_size=8192  
18.*.db_domain=''  
19.*.db_file_name_convert='/home/oracle/app/oracle/oradata/orcl/','/home/oracle/app/oracle/oradata/stdorcl/'  
20.*.db_name='orcl'  
21.*.db_unique_name='stdorcl'  
22.*.db_recovery_file_dest='/home/oracle/app/oracle/fast_recovery_area'  
23.*.db_recovery_file_dest_size=4823449600  
24.*.diagnostic_dest='/home/oracle/app/oracle'  
25.*.dispatchers='(PROTOCOL=TCP)(dispatchers=1)(pool=on)(ticks=1)(connections=500)(sessions=1000)'  
26.*.enable_pluggable_database=true  
27.*.fal_client='stdorcl'  
28.*.fal_server='orcl'  
29.*.inmemory_max_populate_servers=2  
30.*.inmemory_size=157286400  
31.*.local_listener=''  
32.*.log_archive_config='DG_CONFIG=(stdorcl,orcl)'  
33.*.log_archive_dest_1='LOCATION=/home/oracle/arch VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=stdorcl'  
34.*.log_archive_dest_2='SERVICE=orcl LGWR ASYNC  VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=orcl'  
35.*.log_archive_format='%t_%s_%r.arc'  
36.*.log_file_name_convert='/home/oracle/app/oracle/oradata/orcl/','/home/oracle/app/oracle/oradata/stdorcl/'  
37.*.max_dispatchers=5  
38.*.max_shared_servers=20  
39.*.open_cursors=400  
40.*.parallel_execution_message_size=8192  
41.*.pga_aggregate_target=511m  
42.*.processes=300  
43.*.recovery_parallelism=0  
44.*.remote_login_passwordfile='EXCLUSIVE'  
45.*.service_names='ORCL'  
46.*.sga_max_size=1572864000  
47.*.sga_target=1258291200  
48.*.shared_server_sessions=200  
49.*.standby_file_management='AUTO'  
50.*.undo_tablespace='UNDOTBS1'  
```

2、在备库上增加静态监听

```bash
$gedit /home/oracle/app/oracle/product/12.1.0/dbhome_1/network/admin/listener.ora  
```

```bash
1.SID_LIST_LISTENER =  
2.  (SID_LIST =  
3.    (SID_DESC =  
4.      (ORACLE_HOME = /home/oracle/app/oracle/product/12.1.0/db_1)  
5.      (SID_NAME = orcl)  
6.    )  
7.  )  
```

3、重新启动，备库开启实时应用模式

```bash
1.$sqlplus / as sysdba  
2.shutdown immediate  
3.startup  
4.alter database recover managed standby database disconnect;  
```

4、效果测试

![avator](picture\4.png)

