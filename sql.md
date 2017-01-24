# sql 基础 
## 摘要
- DDL Definition 数据定义语言： 用来创建和删除用来存储数据的库和表等对象 create、drop、alert
- DML Manipulation 数据操作语言： 用来查询和变更表中的记录 select、update、insert、delete
- DCL Control 数据控制语言：用来确认和取消对表中数据的变更  commit、rollback、grant、revoke

## 查询基础
- 为列设定别名，as 当为中文时，中文用双引号  
- select distinct a from table; 删除 a 列的重复行，null会保留
- select distinct a,b from table;上去a 、b 列组合后还重复的行
- select 子句可以使用常数和表达式 
- 所有对null 的四则运算，结果都为 null 
- 字符串大小比较按照字典序排列
- 不能对null 使用比较操作符 IS 或者 IS NOT 
- 逻辑运算符 AND OR NOT 

## 聚合和排序
- count(*) 包含 null count(a) 不包含null 
- * 是count 特有的参数，其他聚合函数不能使用 * 
- 聚合函数选某列作为入参时，计算过程会排除 null 的行 ，比如 avg(a) 会排除为null的行，总共10行，null有两个，不会/10,是/8; 
- sum、avg 只对数值型管用，min、max对所有类型都管用
- 想要计算值的种类时，count 参数中可以使用 distinct 
- 在聚合函数中使用 distinct 可以删除重复数据
- group by 聚合键; 当聚合键中包含 null 时，在结果中以"不确定"行的形式表现出来  
- 与聚合函数和 group by 子句有关的常见错误 
  1. 在 select 子句中书写了多余的列，使用聚合函数时，select 子句只能存在以下三种元素 
    1. 常数
    2. 聚合函数
    3. group by 子句中指定的列名（也就是聚合键） 
  2. 在 group by 子句中写了列的别名（不能写别名）  
  3. group by 子句执行结果的顺序是随机的
  4. 只有 select 子句、having 子句以及order by 子句能使用聚合函数，其他不能  
- where 只能针对记录（行）指定条件，having 能针对分组（聚合后）指定条件
- having 子句要写在group by 子句之后  
- having 子句构成要素 1. 常数 2. 聚合函数 3. group by 子句聚合键 （第三类，建议写在 where 子句中）
- 排序键中包含 null 时，会在开头或末尾进行汇总（开头或结尾，不可依赖）  
- order by 中可以使用 select 子句中定义的列的别名  
## 数据更新
### 数据插入
- 省略insert 中的列名，会自动设定为该列的默认值，没有默认值时会设定为null  
- todo p133
  
