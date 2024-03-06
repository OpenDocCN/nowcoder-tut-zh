# 第五章 第 3 节 Explain 优化实战

> 原文：[`www.nowcoder.com/tutorial/10039/fa9050e9869d49aea7707da9fcbde056`](https://www.nowcoder.com/tutorial/10039/fa9050e9869d49aea7707da9fcbde056)

在本节，我们将列举几个 SQL 优化的例子，通过这几个例子，希望可以让读者更加深刻的理解 Explain 工具的作用和使用方法。

## 2.1 分页查询优化

我们创建员工表，并插入 100000 条记录作为演示。

```cpp
-- 示例表
CREATE TABLE `employees` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(24) NOT NULL DEFAULT '' COMMENT '姓名',
  `age` int(20) NOT NULL DEFAULT '0' COMMENT '年龄',
  `position` varchar(20) NOT NULL DEFAULT '' COMMENT '职位',
  `hire_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '入职时间',
  PRIMARY KEY (`id`),
  KEY `idx_name_age_position` (`name`,`age`,`position`) USING BTREE,
  KEY `idx_age` (`age`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=136326 DEFAULT CHARSET=utf8 COMMENT='员工表'

--创建 100000 条记录
drop procedure if EXISTS insert_emp;
delimiter ;;
create procedure insert_emp()
BEGIN
    declare i int;
    set i=1;
    while(i < 100000)DO
        INSERT INTO employees(name,age,position) values(CONCAT('xiaoqiang',i),i,'coder');
        SET i=i+1;
    end WHILE;
end;;
delimiter ;
call insert_emp();
```

当我们分页需要查询从 10000 行开始的 5 条数据时，我们可以执行如下 SQL。

```cpp
select * from  employees WHERE id > 9999 limit 5;
```

![](img/67bea2d65e41cd44c16dbcc800f04be1.png) SQL 语句的返回结果和上面一样，我们利用 explain 输出该 SQL 语句的执行信息。![](img/028acd04985e09c22e5cc41f9ec02043.png) 

可以看到，type 字段的值为 ALL，表明进行了全表扫描。由于 limit 语句前没有加 order by，因此是默认按照主键进行排序的，虽然我们只想要 5 行数据，但 5 行数据是从第 10000 行开始的，因此 mysql 必须读取 10005 行数据，然后抛弃前 10000 行数据。因此要查询一张大表比较靠后的数据，执行效率是非常低的。

分页查询的一个可能优化就是将 limit 查询转换为已知位置的查询。因为我们表的 id 列是主键自增的，因此查询从第 10001 开始的五行数据可改为如下 SQL。

```cpp
select * from  employees WHERE id > 9999 limit 5;
```

SQL 语句的返回结果和上面一样，我们利用 explain 输出该 SQL 语句的执行信息。![](img/9ae1f69662430090a95ebee840d77a44.png)

改写后的 SQL 利用了主键索引，rows 值比前面少了，执行效果会好很多。

如果我们想取出根据 name 排序后的分页查询，则需要执行如下 SQL 语句。  ```cpp
select * from employees order by name limit 9000, 5;
```

![](img/ea6116db01d483fe9943a6e551082c53.png) 我们利用 explain 输出该 SQL 语句的执行信息，结果如下。![](img/c2f4783d22596b512b641794f9af3b6b.png)

我们发现 key 字段的值为 nulL，并没有利用到 idx_name_age_position 索引。这是因为我们需要的数据是完整的行数据，仅仅扫描 idx_name_age_position 索引树并不能找到所有的数据，还要进行回表操作，其成本比直接全表扫描要高，故索引优化器放弃使用索引。为了按照 name 进行排序，可以看到 Extra 的值为 Using filesort，即 mysql 使用了文件外部排序，这是一个很耗时的操作。

为了验证这点，我们看看 explain select name from employees order by name limit 9000, 5 的输出信息。![](img/2676d6a337cefd6c664c01773372c8d2.png)

明显的看到 key 的值为 idx_name_age_position，此时扫描 idx_name_age_position 索引树即可满足我们的数据。

优化前面没有利用索引的 SQL 的做法是：尽可能的利用索引覆盖机制，让排序时返回的字段尽可能的少，所以可以让排序和分页操作先查出主键，然后根据主键查到对应的记录。如执行如下 SQL。

```cpp
select * from employees as e inner join(select id from employees order by name limit 9000,5) as ed on e.id=ed.id;
```

![](img/ae23f3186cf934240a494692869701bf.png)

优化后的 SQL 语句比之前的运行时间少了将近一半。解析 explain 的信息，我们知道执行过程大致如下：先执行 select id from employees order by name limit 9000,5 的子语句取出 id 值，该子语句利用了 idx_name_age_position 索引， 然后在 employees 表中根据这些 id 值取出完整的行记录，该子语句利用了主键索引。

以上所有 SQL 语句的执行时间附图如下：
![](img/f72ef4d42413ce0002c975f695a20889.png)

## 2.2 Order by

接下来，我们看几个 Order by 的例子。首先执行如下 SQL 语句。

```cpp
EXPLAIN select * from employees WHERE name='LiLei' and position='dev' order by age;
```

![](img/8bef575c9b3b4fd3aed0962c848bcf0b.png)通过 explain 的输出信息我们可以知道，该查询利用了 idx_name_age_position 的联合索引，但根据 key_len = 74，我们可以得出查询仅仅使用了 idx_name_age_position 索引的 name 字段，这是因为最左前缀匹配原则。从 Extra 信息我们可以知道，age 索引列用在排序的过程中。 ```cpp
EXPLAIN select * from employees WHERE name='LiLei'  order by position;
```

![](img/68fc3b6a0997a920fdf404bc6399e883.png)
根据 key 和 key_len，我们知道查询利用了 idx_name_age_position 索引的 name 字段，根据最左前缀匹配原则，使用 position 字段进行排序因为跳过了 age 字段，故 Extra 的输出有 Using filesort。 ```cpp
EXPLAIN select * from employees WHERE name='LiLei'  order by age,position;
```

![](img/5a2e0cf498579bb320e7cdbbdbdb877f.png) 

和上一条 SQL 语句不同的是，该查询根据 age、position 字段进行排序，这符合最左前缀原则，故排序阶段也可以利用 idx_name_age_position 索引，所以 Extra 的输出是 Using index condition。

通过这几个例子，我们可以得出优化 Order by 语句的关键就是利用好索引，并且 SQL 语句要匹配最左前缀原则。

## 2.3 总结

通过前两节的例子，相信大家体会了 explain 工具在优化 SQL 查询中的作用，但由于 explain 输出的字段繁多，哪些字段是需要我们重点关注的呢？一般来说，我们需要重点关注如下字段：

*   type 字段。type 字段的值整体表明了语句的执行效率。如果该值为 ALL，即全表扫描，性能是最差的，我们要重点关注该查询，为什么没利用索引，是 SQL 语句的问题还是没有索引可以利用。
*   key 和 key_len 字段。通过这两个字段的组合，我们可以知道查询用了哪个索引，如果是联合索引，使用了索引的哪些字段。
*   Extra 字段。该字段输出了很多额外有用的信息。如当 Extra 输出中有 Using filesort，表明需要额外的排序操作，这样的查询对 CPU 资源消耗大，建议进行优化。

explain 只是一个工具，真正要利用好这个工具还需要和索引优化建立联系。因此在面试中，对于 explain 部分的考察以输出格式、重点字段的讲解为主，毕竟不可能在面试中把全部字段仔仔细细的说一遍。对 explain 的考察往往会引出索引原理，如索引为什么失效，如何高效的利用索引等。
本文到这里就结束了，希望大家食用愉快！
有任何问题欢迎在文章下方留言~