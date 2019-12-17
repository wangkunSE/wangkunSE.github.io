---
title: test
date: 2019-12-10 10:47:29
categories: [test,test1]
tags: test
---

## MySQL必知必会

### 1.检索数据

#### 1.1 distinct

{% codeblock %}

##用于检索出单一的行值
select distinct vend_id from products.

##distinct 不止作用于其后置字段，它会查出所有列组合情况下单一的列。
select distinct vend_id,vend_name from products.

mysql> select * from test;
+----+------+-------+
| id | name | photo |
+----+------+-------+
|  1 | tony | photo |
|  1 | tom  | photo |
|  2 | tony | photo |
|  2 | tom  | photo |
|  2 | tom  | photo |
+----+------+-------+
5 rows in set

mysql> select distinct id,name from test;
+----+------+
| id | name |
+----+------+
|  1 | tony |
|  1 | tom  |
|  2 | tony |
|  2 | tom  |
+----+------+
4 rows in set

mysql> select distinct id,photo from test;
+----+-------+
| id | photo |
+----+-------+
|  1 | photo |
|  2 | photo |
+----+-------+
2 rows in set

{% endcodeblock %}

#### 1.2 多个字段倒序

> 多个字段想使用倒序时，必须为每个字段单独使用倒序，否则不会生效。





{% blockquote [asdasd[, 文章]] [http://localhost:4000/2019/12/10/test2/] [test2] %}
test2222222222222222222222
{% endblockquote %}

{% codeblock [codetest] [lang:java] [url] [link text] %}
System.out.println("hello world");
{% endcodeblock %}