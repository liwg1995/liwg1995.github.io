---
title: MySQL数据库slow log的两种分析方法
date: 2017-11-29 14:43:08
tags: MySQL
categories: 技术
---
### mysqldumpslow
`$ mysqldumpslow -s t -t 5 proddbcrm01_slowlog_20171129.log`
```
Reading mysql slow query log from proddbcrm01_slowlog_20171129.log
Count: 608  Time=0.52s (315s)  Lock=0.00s (0s)  Rows=0.0 (0), focuscrm[focuscrm]@[172.18.16.13]
  select * from `sms_send_tasks` where (`send_at` < 'S' and `status` = N and `send_mode` = N)

Count: 608  Time=0.50s (306s)  Lock=0.00s (0s)  Rows=0.0 (0), focuscrm[focuscrm]@[172.18.16.13]
  select * from `sms_send_tasks` where (`send_at` < 'S' and `status` = N and `send_mode` = N) limit N

Count: 137  Time=0.21s (28s)  Lock=0.00s (0s)  Rows=10.0 (1370), focuscrm[focuscrm]@[172.18.16.15]
  select * from `contacts` where `enterprise_id` = N and `name` like 'S' and `contacts`.`deleted_at` is null order by `updated_at` desc limit N offset N

Count: 135  Time=0.11s (14s)  Lock=0.00s (0s)  Rows=1.0 (135), focuscrm[focuscrm]@[172.18.16.15]
  select count(*) as aggregate from `contacts` where `enterprise_id` = N and `name` like 'S' and `contacts`.`deleted_at` is null

Died at /usr/local/bin/mysqldumpslow line 161, <> chunk 1488.
```
<!--more-->

### mysqlsla
`$ mysqlsla -lt slow proddbcrm01_slowlog_20171129.log`
```
Grand Totals: Time 665 s, Lock 0 s, Rows sent 1.50k, Rows Examined 799.57M


______________________________________________________________________ 001 ___
Count         : 608  (40.86%)
Time          : 315.328047 s total, 518.632 ms avg, 468.31 ms to 592.164 ms max  (47.41%)
  95% of Time : 297.713149 s total, 515.967 ms avg, 468.31 ms to 561.262 ms max
Lock Time (s) : 21.357 ms total, 35 �s avg, 24 �s to 62 �s max  (39.48%)
  95% of Lock : 19.822 ms total, 34 �s avg, 24 �s to 45 �s max
Rows sent     : 0 avg, 0 to 0 max  (0.00%)
Rows examined : 637.98k avg, 637.98k to 637.98k max  (48.51%)
Database      :
Users         :
	focuscrm@ 172.18.16.13 : 100.00% (608) of query, 81.72% (1216) of all users

Query abstract:
SET timestamp=N; SELECT * FROM sms_send_tasks WHERE (send_at < 'S' AND status = N AND send_mode = N);

Query sample:
SET timestamp=1511884922;
select * from `sms_send_tasks` where (`send_at` < '2017-11-29 00:02:01' and `status` = 0 and `send_mode` = 0);

...
```
