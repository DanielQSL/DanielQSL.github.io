---
title: MySQL字符集和排序规则
date: 2020-06-16 20:12:22
categories: 
- MySQL
tags:
- MySQL
---



> 最近项目有emoji表情相关的查询和比较，遇到一些坑，故记录一下。

# utf8 与 utf8mb4

utf8编码是一种变长的编码机制，可以用1~4个字节存储字符。

​		MySQL 中的 utf8 编码并不是真正的 UTF-8，而是阉割版的，最长只有3个字节。当遇到占4个字节的 UTF-8 编码，例如 emoji 字符或者复杂的汉字，会导致存储异常。

​		MySQL在 5.5.3 之后增加了 utf8mb4 字符编码，mb4即 most bytes 4,使用4个字节来表示完整的UTF-8。简单说 utf8mb4 是 utf8 的超集并完全兼容utf8，能够用四个字节存储更多的字符。

​		utf8mb4 已成为 MySQL 8.0 的默认字符集，在MySQL 8.0.1及更高版本中将 utf8mb4_0900_ai_ci 作为默认排序规则。



# 排序字符集 COLLATE

**utf8mb4_bin:** 将字符串每个字符用二进制数据编译存储，区分大小写，而且可以存二进制的内容。

**utf8mb4_general_ci:**ci即case insensitive，不区分大小写。是一个遗留的校对规则，不支持扩展，它仅能够在字符之间进行逐个比较，没有实现Unicode排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致。但是，在绝大多数情况下，这些特殊字符的顺序并不需要那么精确。

**utf8mb4_unicode_ci:**是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序，Unicode排序规则为了能够处理特殊字符的情况，实现了略微复杂的排序算法。

都带有`_ci`字样，这是Case Insensitive的缩写，即大小写无关；相反，对于那些`_cs`后缀的`COLLATE`，则是Case Sensitive，即大小写敏感的。

MySQL**默认**的字符检索策略：utf8mb4_general_ci（5.7）、utf8mb4_0900_ai_ci（8.0.1）

MySQL 8.0 默认的是 utf8mb4_0900_ai_ci，属于 utf8mb4_unicode_ci 中的一种，具体含义如下：

- uft8mb4 表示用 UTF-8 编码方案，每个字符最多占4个字节。
- 0900 指的是 Unicode 校对算法版本。（Unicode归类算法是用于比较符合Unicode标准要求的两个Unicode字符串的方法）。
- ai指的是口音不敏感。也就是说，排序时e，è，é，ê和ë之间没有区别。
- ci表示不区分大小写。也就是说，排序时p和P之间没有区别。

utf8mb4_0900_ai_ci排序规则现在是默认排序规则，因此默认情况下新表格可以存储基本多语言平面之外的字符。现在可以默认存储表情符号。如果需要重音灵敏度和区分大小写，则可以使用utf8mb4_0900_as_cs代替。



# 相关SQL语句

查看数据表字符集编码

```sql
SHOW FULL COLUMNS FROM table_name;
```



修改表字符集编码

```sql
ALTER TABLE `table_name` CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
```



修改表中某列的字符集编码

```sql
ALTER TABLE `table_name` CHANGE field field VARCHAR(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
```

