---
title: "Linux crontab"
date: 2019-12-11
draft: false
author: "Iven"
---

crontab 可以创建循环任务
<!--more-->
**使用**
```text
crontab [-u username] [-l|-e|-r]
选项与参数：
-u  ：只有root用户才能用这个命令，仅查看此用户的crontab的内容
-e  ：编辑 crontab 的内容
-l  ：查阅 crontab 的内容
-r  ：移除所有的 crontab 的内容，若仅要移除一项，请用 -e 去编辑。
```

**相关文件**
/etc/cron.allow: 将可以使用 crontab 的帐号写入其中，若不在这个文件内的使用者则不可使用 crontab  
/etc/cron.deny: 将不可以使用 crontab 的帐号写入其中，若未记录到这个文件当中的使用者，就可以使用 crontab  
/var/spool/cron/: 用户创建的crontab都被写到这个目录下，比如root用户创建的，就会产生一个文件/var/spool/cron/root来保存root用户创建的cron  
/var/log/cron: crontab执行的日志记录


**cron的语法**
```text
# Example of job definition:
# .---------------- 分钟 (0 - 59)
# |  .------------- 小时 (0 - 23)
# |  |  .---------- 日期 (1 - 31)
# |  |  |  .------- 月份 (1 - 12)
# |  |  |  |  .---- 星期 (0 - 6) (周日=0 或者 7) 
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed

* 星号：表示任何时刻都可以接受的意思
, 逗号：表示多个时段的意思 ，比如15点和16点执行任务 就是：0 15,16 * * * 
- 减号：代表时间段范围内，比如8点到12点之间的每小时的20分钟都进行一项工作：20 8-12 * * *
/n 斜线：每隔n个间隔单位, 比如每五分钟进行一次 */5 * * * * 
```