# 一、InnoDB和MyISAM的区别

## 1.1 事务

InnoDB支持事务，而MyISAM不支持事务。

## 1.2 外键

InnoDB支持外键，而MyISAM不支持外键。对一个包含外键的InnoDB表转化为MyISAM会失败。

## 1.3 索引

