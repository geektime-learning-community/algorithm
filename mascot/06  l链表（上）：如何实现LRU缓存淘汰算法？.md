# 06 | l链表（上）：如何实现LRU缓存淘汰算法？

## 链表结构

链表是用指针把零散的内存块组织在一起的数据结构。

### 单链表

每个链表节点都有一个后继指针( next )，第一个节点称为头节点，最后一个节点是尾节点。头节点用来记录链表的基址，尾节点的后置指针指向一个空地址( null )。

### 循环链表

循环链表是一种特殊的单链表，唯一区别就是尾节点指向的是链表的头节点。循环链表的优点是从链尾到链头比较方便。

### 双向链表

单链表只有一个方向，双向链表支持两个方向。每个节点除了有后继节点外，还有一个前驱结点( prev )。双向链表有两个指针，比单向链表更耗内存，但因为他支持双向操作，比单链表更灵活。

## 删除操作

删除链表常见情况：

+ 删除节点中“值等于某个给定值”的节点
+ 删除给定指针指向的节点

对于第一种情况，不管是单链表还是双链表，都需要依次遍历对比，直到找到给定值的节点，然后删除。删除操作时间复杂度是 O(1)，但是遍历操作时间复杂度是 O(n)，所以总时间复杂度是 O(n)。

对于第二种情况，已经知道要删除的节点了。但是要删除一个节点需要知道其前驱结点，对于单链表来说，不能直接获取其前驱结点，需要从头遍历，遍历的时间复杂度是O(n)。对于双链表来说，应为有前驱指针，所以可以直接获取前驱结点。时间复杂度是 O(1)。

插入操作同删除操作一样，双向链表在删除和插入上比单链表更高效。

> 这是一种空间换时间的设计思想。当空间不足时也可以用时间换空间。

## 链表和数组性能比较

数组简单易用，需要连续的内存空间，可以借助 CPU 的缓存机制，预读数组数据，所以访问效率高。而链表不连续，CPU 没有办法预读。

数组的缺点是大小固定，一旦声明就要占用整块内存空间。声明过大，可能会导致“内存不足( out of memory)”，声明过小,可能会出现不够使用的情况。这时需要重新申请，把旧数组拷贝过去。

## 用链表实现 LRU 缓存淘汰算法



思路是这样的：我们维护一个有序单链表，越靠近链表尾部的结点是越早之前访问的。当有一个新的数据被访问时，我们从链表头开始顺序访问链表。

1. 如果此数据之前已经被缓存在链表中了，我们遍历得到这个数据对应的结点，并将其从原来的位置删除，然后再插入到链表的头部。
2. 如果此数据没有在缓存链表中，又可以分为两种情况：
   + 如果此时缓存未满，则将此结点直接插入到链表的头部；
   + 如果此时缓存已满，则链表尾结点删除，将新的数据结点插入链表的的头部。

