# 第五章 第 1 节 Explain 精讲（上）

> 原文：[`www.nowcoder.com/tutorial/10039/c181f6d2264f4c259378e005c8b34b6c`](https://www.nowcoder.com/tutorial/10039/c181f6d2264f4c259378e005c8b34b6c)

## 1.1 Explain 的作用

在实际工作中，我们往往需要分析一条查询语句为什么执行的如此之慢，这时候 Explain 工具就会派上用场。Explain 工具可以用来获取一条查询语句的执行计划相关信息，对输出的信息进行分析，我们大概可以知道查询是如何执行的，在此基础上，我们就可以找到速度慢的原因了。

这里提到了一个专业术语——“一条查询语句的执行计划”，我觉得还是很有必要对执行计划进行解释。一条查询语句可能有多种执行路径得到最后相同的结果，但执行不同的路径需要的成本不同，Mysql 的查询优化器会为这条查询找到最好的执行路径，这就是执行计划。如执行 select * from user where id = 1，可以通过全表扫描，并根据 id 进行过滤得到符合条件的数据，如果在 id 字段上建立了索引，也可以通过索引得到数据。这就有了两种不同的执行路径，很明显第二条执行路径更优，因此 Mysql 查询优化器为这条 SQL 查询语句生成的执行计划就是采用第二条路径执行。

一条 SQL 查询语句可以产生多个执行计划，那 Mysql 是如何选择最好的执行计划呢？Mysql 有一个部件，查询优化器，就是干这件事情的。Mysql 使用基于成本的查询优化器，它将尝试预测一个查询使用某种执行计划时的成本，并选择其中成本做小的一个。成本最初定义为随机读取一个 4K 数据页的成本，后面成本的计算变得越来越复杂，其中引入了一些因子来估算某些操作的代价。我们可以使用 show status like 'last_query_cost' 语句来查找最后一次查询语句的执行计划成本。

![](img/d70793e1e63ce28dca8a0aa48b91ed07.png)

下图是 Mysql 查询执行的整体路径图，Explain 重点关注的对象就是图中的查询执行计划。通过 Explain 工具我们得到执行计划相关的信息，如查询是否利用了索引，利用了那个索引，利用这些信息，我们可以对 SQL 语句的性能问题进行分析并加以优化。

![](img/e2017487ecf2a7eac6a8572368c7e81a.png)

## 1.2 Explain 输出详解

要使用 Explain 工具也很简单，这需要把 Explain 关键字放在查询语句的前面就可以了。为了进行演示，我们创建人员信息表 user_info 和订单表 order_info。

```cpp
CREATE TABLE `user_info` (
  `id`   BIGINT(20)  NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(50) NOT NULL DEFAULT '',
  `age`  INT(11)              DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `name_index` (`name`)
)ENGINE = InnoDB,DEFAULT CHARSET = utf8;

INSERT INTO user_info (name, age) VALUES ('xys', 20);
INSERT INTO user_info (name, age) VALUES ('a', 21);
INSERT INTO user_info (name, age) VALUES ('b', 23);
INSERT INTO user_info (name, age) VALUES ('c', 50);
INSERT INTO user_info (name, age) VALUES ('d', 15);
INSERT INTO user_info (name, age) VALUES ('e', 20);
INSERT INTO user_info (name, age) VALUES ('f', 21);
INSERT INTO user_info (name, age) VALUES ('g', 23);
INSERT INTO user_info (name, age) VALUES ('h', 50);
INSERT INTO user_info (name, age) VALUES ('i', 15);

CREATE TABLE `order_info` (
  `id`           BIGINT(20)  NOT NULL AUTO_INCREMENT,
  `user_id`      BIGINT(20)           DEFAULT NULL,
  `product_name` VARCHAR(50) NOT NULL DEFAULT '',
  `productor`    VARCHAR(30)          DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `user_product_detail_index` (`user_id`, `product_name`, `productor`)
)ENGINE = InnoDB,DEFAULT CHARSET = utf8;

INSERT INTO order_info (user_id, product_name, productor) VALUES (1, 'p1', 'WHH');
INSERT INTO order_info (user_id, product_name, productor) VALUES (1, 'p2', 'WL');
INSERT INTO order_info (user_id, product_name, productor) VALUES (1, 'p1', 'DX');
INSERT INTO order_info (user_id, product_name, productor) VALUES (2, 'p1', 'WHH');
INSERT INTO order_info (user_id, product_name, productor) VALUES (2, 'p5', 'WL');
INSERT INTO order_info (user_id, product_name, productor) VALUES (3, 'p3', 'MA');
INSERT INTO order_info (user_id, product_name, productor) VALUES (4, 'p1', 'WHH');
INSERT INTO order_info (user_id, product_name, productor) VALUES (6, 'p1', 'WHH');
INSERT INTO order_info (user_id, product_name, productor) VALUES (9, 'p8', 'TE');
```

执行 explain select * from user_info where id = 1 , 输出信息如下。为了更好的理解 Explain 输出的信息，下面对输出的各个字段进行详细的解释。

![](img/677a1b62f0a7a55eb82660c5a2f1c17f.png)

### 1.2.1 id

explain 输出可能有多行，因为整个查询语句可能有多个子查询，explain 会输出每个子查询的执行计划相关信息。explain 输出的每一行代表一个子查询的执行计划信息，其中每一行都有一个 id 列来唯一标识。id 列除了用来唯一标识不同的子查询外，id 值的大小和行的输出先后反应了不同子查询的执行优先级。具体规则如下：

*   id 值越大，对于的子查询的执行优先级越高；
*   id 相同，前面输出的子查询执行优先级高。

假设我们有这样一个需求：输出买过产品名字为 p1 的所有用户的姓名。如下 SQL 语句即可完成，我们来看看这条查询语句 explain 的输出。

```cpp
explain select name from user_info where id in (select user_id from order_info where product_name = 'p1');
```

![](img/aac7961077659d2a7ca3531ec10feffd.png)

可以看到输出有两行，虽然 id 值相同，但根据规则二，第一行对于的子查询的执行优先级比第二行高。第一行对应的子查询很明显是在 order_info 表中查找，第二行对应的子查询是在 user_info 表中查询，其实分析原始 SQL 语句，我们不难理解为什么要先在 order_info 表查找，然后在 user_info 表中查找。
![](img/1cb3e39dd294b18259ecbb606e5f5112.png)

 ### 1.2.2 select_type

select_type 字段表明本行对应的查询是简单查询还是复杂查询。该字段有多个值，下面一一举例说明。

**SIMPLE**

该值表示本行对应的查询是简单查询，所谓简单查询就是查询不包含子查询和 UNION。如执行 explain select * from user_info where id = 1 语句。

**PRIMARY**

该值表示本行对应的查询是复杂查询，可能依赖多个子查询。假设我们有这样的需求：在购买了产品名为 p8 的用户中，找出 user_id 最大的用户姓名。如下 SQL 语句可以满足这个需求。 

```cpp
explain select name from user_info where id = (select max(user_id) from order_info where product_name = 'p8');
```

![](img/faf11055e863161354e6cd598418b688.png)

可以看到第一行的 select_type 为 PRIMARY，表明该行对应查询为复杂查询，这是因为该查询包含了另外一个子查询。

当某一行的 select_type 为 PRIMARY 时，表明整个 SQL 语句的最外层为复杂查询，其他子查询的 select_type 字段有如下取值：

1.  SUBQUERY。该值表明对于的查询一般是位于 where 子句中的子查询，如上列所示。
2.  DERIVED。该值表明对于的查询是位于 form 中的子查询，一般会产生一张临时表，也被成为派生表。
3.  UNION。该值表明对于的查询是位于 union 之后的子查询。
4.  UNION RESULT。该值表明对应的查询是从 UNION 临时表获取结果的子查询。

如执行 explain select id from user_info union select user_id from order_info，输出信息如下。
![](img/26f2c4775a99ec4bb2a39ac558f60fcf.png)

其中 id 为 2 的查询的 select_type 为 UNION,该查询位于 union 之后。最后一个子查询的 select_type 为 UNION RESULT，是从 UNION 临时表获取最后的结果。