+++ 
author = "Xi Jiang"
title = "InnoDB storage engine basic architecture review"
date = "2021-06-27"
description = "Dive into mySQL core"
tags = [
    "Database","mySQL"
]
thumbnail = "images/mySQL.png"
+++

After MySQL has done the optimization work, the component that actually executes the SQL statements is the storage engine. Among MySQL's many engines, InnoDB is currently the default storage engine that we widely use. 

InnoDB uses a B+Tree structure for its indexes. A B+Tree is particularly efficient when data doesn’t fit in memory and must be read from the disk, as it ensures that a fixed maximum number of reads would be required to access any data requested, based only on the depth of the tree, which scales nicely.

Reference: https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/


