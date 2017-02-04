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
- 从其他表复制数据 ``` insert a xx select xx from b where xx group by xx  ```  
- insert 语句的 select 语句中，可以使用 where 子句或者 group by 子句等任何 sql 语法。（但使用 order by 子句不会产生任何效果） 

### 数据删除
- delete 语句删除的对象并不是表或者列,而是记录（行）
- 可以通过where子句指定对象条件来删除部分数据

### 数据更新
- 格式 update <表名> set <列名> = <表达式>   

## 复杂查询
### 视图  
- 视图和表的区别在于“是否保存了实际的数据”  
- 表中存储的是实际数据，而视图中保存的是从表中取出数据所使用的select 语句 
- 应该将经常使用的select语句做成视图，视图中的数据会随着原表的变化自动更新（这个是怎么保证的？）  
- 如果每次参照视图，都重新执行select 语句，那么使用视图提高效率从何说起？
- 格式 ``` create view 视图名称(<视图列名1>,<视图列名2>,....) as <select 语句>```  
- 应该避免在视图的基础上创建视图  
- 定义视图时不能使用 order by 子句 ，因为视图和表一样，数据行都是没有顺序的。
- 除了以下条件外，不能对视图进行更新，总结一句话就是：对视图的更新，如果不能明确的反映到某张表的某几列，那么就会更新失败。 
  1. select 子句中未使用distinct 
  2. from 子句中只有一张表
  3. 未使用 group by 子句
  4. 未使用 having 子句 
- 视图和表需要同时进行更新，因此通过聚合得到的视图无法进行更新。  

### 子查询 
- 子查询就是一次性的视图
- 子查询作为内层查询首先执行
- 标量子查询就是返回单一值的子查询（表中的某单元格，或者某聚合后的值）,绝对不能返回多于一行的值<br> 
例如: ``` select a,b,c (select avg(dd) from table1 group by cc) as d from table2; ```   会执行错误。 
- 标量子查询位置，能够使用常数或者列名的地方，无论是select 子句，group by 子句、having子句还是order by 子句，几乎所有的地方都可以使用，<br>
例如: ``` select a,b,c (select avg(d) from table1) as dd from table2; ```  

### 关联子查询 
- 在细分的组内进行比较时，需要使用关联子查询。<br> 
例如：``` select a,b,c from table1 as t1 where c > (select avg(c) from table1 as t2 where t1.d = t2.d group by d); ```  
- **关联子查询也是用来对集合进行切分的**。   
- 结合条件（在子查询中添加的 where 子句的条件）一定要写在子查询中  

## 函数、谓词、Case表达式 
### 函数 
- 函数的种类
  1. 算数函数（用来进行数值计算的函数）
  2. 字符串函数（用来进行字符串操作的函数）
  3. 日期函数（用来进行日期操作的函数）
  4. 转换函数（用来转换数据类型和值的函数）
  5. 聚合函数（用来进行数据聚合的函数） 
- 一些需要注意的函数 
  1. 字符串拼接 str1 || str2 ，concat(str1,str2), str1+str2 
  2. length()注意多字节字符（中文，日文等） 
  3. cast(转换前的值 as 想要转换的数据类型) 
  4. coalesce(数据1,数据2,数据3...) 返回可变参数列表中左侧开始第一个不是null 的值 

### 谓词 
- 谓词是满足特定条件（返回值为真值[true/false/unknown]）的函数。 
- like 字符串的部分一致性查询。 _ 一个字符，% 0-多个字符
- between .. and  范围查询  

### case 表达式
- 格式： 
``` case when <判断表达式> then <表达式>   
         when <判断表达式> then <表达式>   
         when <判断表达式> then <表达式>   
         ...  
         else <表达式>  
    end  
```

- else 子句可以省略，建议不要省略；end不能省略
- case 表达式支持嵌套 

## 集合运算  
### 表的加减法  
- 表的加法 union 并集
- 集合运算符会除去重复的记录  
- 注意事项： 
  1. 作为运算对象的记录的列数必须相同（可以是不同列）
  2. 作为运算对象的记录中的列的类型必须一致
  3. 可以使用任何select语句，但order by 子句只能在最后使用一次。  
- 包含重复记录的集合运算--all 选项 （union all）
- 选取表中公共部分（交集） intersect 
- 记录的减法（差集） except （注意先后顺序） 

### 联结（以列为单位对表进行联结）join  
内外之分，外部：包含元表中不存在（在元表之外）的信息，相反，只包含表内信息的联结被称为内联结  
#### 内联结  
- 选取出同时存在于多张表中的数据
- 进行联结时需要在from 子句中使用多张表
- 进行内联结时必须使用 on 子句，并且要书写在from和where 之间
- 使用联结时，select 子句中的列需要按照<表的别名>.<列名> 的格式进行书写   
#### 外联结  
- 选取出单张表中全部的信息   
- 把哪张表当做主表，外联结的结果就会包含哪张表的全部信息。 
- 指定主表的关键字是 left 和 right ，使用left 时from子句中卸载左侧的表是主表，使用right 时写在右侧的表是主表。   
#### 交叉联结  
- 极少使用 

## SQL高级处理  
### 窗口函数 
OLAP 函数， 对数据库数据进行实时分析处理  
- <窗口函数> over ([partition by <列清单>] order by <排序用列清单>)  
  1. partition by 设定排序的对象范围，对表进行横向分组
  2. order by 指定按照那一列、何种顺序进行排列，对每一组进行纵向排序   
- 能够作为窗口函数使用的函数   
  1. 能够作为窗口函数的聚合函数(sum,avg,count,max,min) 
  2. rank,dense_rank,row_number 等专用窗口函数，专用窗口函数无须参数，括号中都是空的  
- rank 是用来计算记录排序的函数 
  1. rank 计算排序时，如果存在相同位次的记录，则会跳过之后的位次 1.1.1.4  
  2. dense_rank 即使存在相同位次的记录，也不会跳过之后的位次 1.1.1.2..
  3. row_number 赋予唯一的连续位次 1.2.3.4
 
- 通过partition by 分组后的记录合称为"窗口"   
- 原则上窗口函数只能在select子句中使用
- ``` select avg(a) over (order by id rows 2 preceding) as moving_avg from table1; ```  
- rows n preceding 截止到之前 n 行，向上最靠近的n+1行（包括当前行）。 
- rows n following 截止到之后 n 行，向后最靠近的n+1行（包括当前行）。
- 当聚合函数作为窗口函数使用时，会以当前记录的基准来决定统计对象的记录 
  
  
  
  
  
  
