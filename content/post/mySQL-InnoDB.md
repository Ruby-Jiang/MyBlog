+++ 
author = "Xi Jiang"
title = "InnoDB storage engine page structure review"
date = "2021-06-27"
description = "Dive into mySQL core"
tags = [
    "Database","mySQL"
]
thumbnail = "images/mySQL.png"
+++

After MySQL has done the optimization work, the component that actually executes the SQL statements is the storage engine. Among MySQL's many engines, InnoDB is currently the default storage engine that we widely use. 

InnoDB uses a B+Tree structure for its indexes. A B+Tree is particularly efficient when data doesn’t fit in memory and must be read from the disk, as it ensures that a fixed maximum number of reads would be required to access any data requested, based only on the depth of the tree, which scales nicely[^1].

<p align="center">
    <img src="../../images/B+tree.png" width="600" />
</p>

### MySql record storage

#### Header

The control information of the current page is recorded, including the page pointers of the left and right sibling pages (the left and right pointers of the B+ tree) and page usage.

#### Dummy record

Take the clustered index as an example: the largest virtual record of the page (larger than the primary key of the page memory) and the smallest virtual record (smaller than the primary key of the page memory) are saved, which is convenient for query (also the structure of the B+ tree)

#### Record heap

Row record storage block (including stored and deleted records), these record heaps are all in leaf nodes, non-leaf nodes do not store data and only store indexes (this is also the benefit of B+ trees)

#### Free space linked list

Linked list of deleted records

#### Unallocated space

Unused space

#### Slot area

The page links the information of other pages through the two pointers on the left and right of the head, so there will be many linked lists. Then how to find the required records from so many linked lists is to point to a certain page through the pointer information in the slot area memory, such as dichotomy , Divided into many blocks, each slot points to the corresponding block, which also involves the use of traversal and dichotomy when MySql queries in the industry

#### Footer

As long as it is the verification information of the storage page

<br>

### MySql maintaining in-page records

#### Order guarantee

There are two order guarantees

1. Physically orderly 

2. Logical order (MySql's choice) 


#### Insertion strategy

Linked list of unallocated space and free space in Page

#### In-page inquiries

traversal and binary search (using Slot area)


[^1]: https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/


