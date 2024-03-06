# 第四章 第 1 节 面试高频考题

> 原文：[`www.nowcoder.com/tutorial/10039/94ebf5fa29fb4fdf9ba86eda2dceb35a`](https://www.nowcoder.com/tutorial/10039/94ebf5fa29fb4fdf9ba86eda2dceb35a)

这一节向大家展示几个面试中常见的问题，我们的目地是通过问题能够深入掌握知识，并不是让大家记住应对答案。大家可通过文中的解析，再自己动手实践然后整理出适合自己的标准答案。

## 3.1 一个查询语句慢，从哪些方面查找原因

首先，我们如何感知一个查询语句慢。有如下两种途径：

1.  手动感知。在查询前后，我们插入时间戳，计算两次的时间戳，看看时间是不是比较长。
2.  利用慢查询日志。MySql 提供了慢查询日志的功能，可以设一个阈值，将运行时间超过该值的所有 SQL 语句都记录到慢查询日志文件中。该阈值可以通过参数 long_query_time 来设置，默认值为 10，代表 10 秒。

当知道一条 SQL 语句查询较慢，有哪些比较好的查找原因的思路呢？我的建议如下：

*   数据量大。这是最直观的原因。如果都是进行全表扫描操作，一个 100 行的表和一个 100 万行的表的执行时间肯定不同。
*   没有使用索引。例如你的条件是 where c = 1，但你没有在 c 字段建立索引，导致了全表扫描，这当然也会导致查询慢。
*   索引失效。你记得明明在字段 c 上建立了索引，你的 SQL 语句的查询条件也是 where c **,但查询依然很慢，你用 explain 查看执行过程，发现查找居然没走索引，这就是索引失效的场景。下一个问题，我们来详细谈谈哪些错误的用***导致索引失效。

## 3.2 索引失效的场景

我们先来创建一张表，并插入 100000 行数据，以此来证明索引是不是真的失效。

```cpp
CREATE TABLE `user` (
  `id` int(11) NOT NULL,
  `name` varchar(45) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1

delimiter ;;
create procedure idata()
begin
  declare i int;
  set i=1;
  while(i<=100000) do
    insert into user values(i, 'lc', i);
    set i=i+1;
  end while;
end;;
delimiter ;

call idata();
```

为了看到每条语句的查询时间，我们执行 set profiling=1。首先我们执行 select * from user where age = 2; 执行 show profiles; 可见执行耗时为 0.04 秒。
![](img/7431e4494d89d34189eaa85cc8a6e07b.png)
我们在 age 上增加一个索引，ALTER TABLE ***user*** ADD INDEX index_age ***(age)***; 再执行 select * from user where age = 2; 执行耗时为 0.003 秒，可见索引起来效果。![](img/9c39c1c0f1576ee3bd2e059572971384.png)
下面我们来看几种索引失效的场景：

*   对索引列进行运算导致索引失效。 执行 select * from user where age - 1 = 1; SQL 执行耗时为 0.06 秒，可见索引失效。这是因为索引对应的 B+树的关键字是索引的原值，查找过程是直接对原值进行比较，不会设计运算操作，因此导致了索引失效。

![](img/4efd7426a1162f3d23b220b64a51dd82.png)

*   使用 or 但是条件中有列没有加索引，则会导致其他索引失效。 select * from user where age = 2 or name = 'ab'; 执行耗时为 0.049 秒。这是因为虽然 age 列有索引，但 name 列没有索引，SQL 语句的执行时间决定与对 name 列的查询，故导致 age 的索引失效。

![](img/3688e13d962bdf1927dd52ad7fcba951.png)

*   不符合最左前缀原则的查询，这一部分具体可看 联合索引 那一节。

## 3.3 InnoDB 中一棵 B+树可以存放多少行数据

我们知道在 InnoDB 中，表都是根据主键顺序以索引的形式存放的，这种存储方式的表称为索引组织表。索引的数据结构是 B+树，一张人员表（id 为主键，有 name 和 age 字段）的存储示意图如下：

![](img/7da2af6e07463a4c992578a2deb62aff.png)查询记录的时候，会先根据根索引页选择对应的子树，如果找到的节点还是索引页，再选择对应的子树，直到到达的节点为数据页，根据主键在数据页中进行二分查找定位到记录。如 select * from user where id = 4。查找根索引页 3，根据页指针 p5 定位到数据页 5，在数据页 5 中根据 id 为 4 进行二分查找，最后得到具体的行数据。

这里涉及一些概念：

*   页。在 InnoDB 存储引擎中，无论是索引的存放还是具体行数据的存放都是按页存放的。如果页中存放的是索引，则称为索引页，如果页中存放的是数据，则称为数据页。页的大小默认为 16KB。因此，如果一个行记录大小为 1KB，则一个数据页只能存放 16 个行记录。通过执行 show variables like 'innodb_page_size'可以查看当前页的配置大小。

![](img/9283d18a7164284e131e5eff0676edca.png)

*   根索引页。InnoDB 的每张表都有一个根索引页。我们对数据的查询，总是先通过根索引页然后再往下索引。使用如下 SQL 语句可以查询当前数据库中 innoDB 存储引擎的根索引页。

```cpp
SELECT
b.name, a.name, index_id, type, a.space, a.PAGE_NO
FROM
information_schema.INNODB_SYS_INDEXES a,
information_schema.INNODB_SYS_TABLES b
WHERE
a.table_id = b.table_id AND a.space <> 0;
```

![](img/a9a89678b85e3867794adb960fe8a465.png)

其中 PAGE_NO 字段就是一颗 B+树的根索引页号。在 innoDB 中，一个索引就是一颗 B+树，主键索引的叶子节点（数据页）存放完整的行数据，辅助索引的叶子节点（数据页）存放主键值。从上述结果可以看到，主键索引根页的 page number 均为 3，而其他的辅助索引的根索引页 page number 为 4。

OK，现在回到我们最初的问题，InnoDB 中一棵 B+树可以存放多少行数据？假设现在 B+树的高度为 2，即存在一个根节点和若干个叶子节点，那么这棵 B+树的存放总记录数为：根节点指针数*单个叶子节点记录行数。

再假设一个行记录为 1KB，则一个叶子节点（数据页）可存放 16K/1K=16，假设主键 ID 为 bigint 类型，长度为 8 字节，而指针大小在 InnoDB 源码中设置为 6 字节，这样一共 14 字节，我们一个索引页中能存放多少这样的单元，其实就代表有多少指针，即 16384/14=1170。那么可以算出一棵高度为 2 的 B+树，能存放 1170*16=18720 条这样的数据记录。

根据同样的原理我们可以算出一个高度为 3 的 B+树可以存放：1170 * 1170 *16=21902400 条这样的记录。所以在 InnoDB 中 B+树高度一般为 1-3 层，它就能满足千万级的数据存储。在查找数据时一次页的查找代表一次 IO，所以通过主键索引查询通常只需要 1-3 次 IO 操作即可查找到数据。

这个问题的逆问题就是一张表有 xx 行记录，问你 B+树的高度是多少，如果弄明白了这个问题，它的逆问题应该很简单，这个就留给大家思考了。