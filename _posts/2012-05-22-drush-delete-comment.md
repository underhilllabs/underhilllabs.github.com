---
layout: post
title: Using Drush and SQL to Delete Spam Comments
tags: drupal drush sql
category: Drupal
description: Using the drush commands drush sqlq and drush sql-cli and some sql to delete spam comments
---

> spam filters fall apart; the centre cannot hold.

### Spam Gets By ...

From time to time, despite precautions, spammers get by the Drupal filters and
captchas.  Or perhaps, the problem gets put in your lap and you have
the pleasure of deleting thousands of spam comments.  

The Drupal Administrative interface doesn't provide many resources unless you enjoy clicking
"select all" and "delete selected comments", page after page, 50 comments at a time.

### Drush to the Rescue

But, if you are comfortable with sql, one easy solution is to use __drush sql-query__  to delete the comments.

### Before you start Deleting anything...

If you are unfamiliar at all with sql, first back up your drupal database.

```
drush sql-dump --result-file=/path/to/dump-file.sql
```

You could also dump just the comment table. (The '~' says to save it
in your home directory.) 

```
drush sql-dump --result-file=~/comment-table.sql --tables-list=comment
```

### Now let's get busy!

#### First take a look at the comment table:

```
drush sqlq "desc comment"
```

( __drush sqlq__ is the short form of __drush sql-query__)

You could also use drush sql-cli to view the comment table:

<pre>
$ drush sql-cli
....
mysql> desc comment;
+----------+---------------------+------+-----+---------+----------------+
| Field    | Type                | Null | Key | Default | Extra          |
+----------+---------------------+------+-----+---------+----------------+
| cid      | int(11)             | NO   | PRI | NULL    | auto_increment |
| pid      | int(11)             | NO   | MUL | 0       |                |
| nid      | int(11)             | NO   | MUL | 0       |                |
| uid      | int(11)             | NO   | MUL | 0       |                |
| subject  | varchar(64)         | NO   |     |         |                |
| hostname | varchar(128)        | NO   |     |         |                |
| created  | int(11)             | NO   | MUL | 0       |                |
| changed  | int(11)             | NO   |     | 0       |                |
| status   | tinyint(3) unsigned | NO   |     | 1       |                |
| thread   | varchar(255)        | NO   |     | NULL    |                |
| name     | varchar(60)         | YES  |     | NULL    |                |
| mail     | varchar(64)         | YES  |     | NULL    |                |
| homepage | varchar(255)        | YES  |     | NULL    |                |
| language | varchar(12)         | NO   |     |         |                |
+----------+---------------------+------+-----+---------+----------------+
14 rows in set (0.00 sec)
</pre>


#### Next take a look at the comments:

```
drush sqlq "select subject,cid from comment "
```

#### You can show comments from the last 30 days with the following.


The created field is stored as a unix timestamp (seconds since the
Unix Epoch), so to get last month timestamp: 

```
Seconds to subtract = (seconds in an hour)*(24 hours in a day)*(30 days) = 3600*24*30 
```

```
drush sqlq "select subject,cid from comment where 
                         created > unix_timestamp(now())-3600*24*30"
```

###  Delete comments within a range of comment ids.
If you know the range of spam comment ids, you can give sql a range of comment ids:

Show the range of comments:


```
drush sqlq "select subject,cid from comment where cid > 3013 and cid < 3134 "
```

Delete the range of comments:


```
drush sqlq "delete from comment where cid > 3013 and cid < 3134 "
```

### Show comments, group by name of commenter


```
drush sqlq "select subject,name,cid from comment where cid > 300 group by name"
```

### But, honestly, there's probably a drush delete-comments module that needs to be written!

I'll keep you posted of my progress on that. In the meantime, you can try using the previous commands.  Just remember to backup your database first, its only a single drush command after all!

```
drush dump --result-file=~/drupal-backup.sql
```

