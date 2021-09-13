# 一、日期处理
## 1. LocalDate、LocalTime和LocalDateTime

* LocalDate用于表示日期，常用方法包括:
  * now
  * of
  * getYear
  * getMonth
  * getDay
  * getDayOfWeek
  * lengthOfMonth
  * isLeapYear等
* LocalTime用于表示时间，常用方法类似于LocalDate
* LocalDateTime是 LocalDate和LocalTime的集合体，用于精确表示某个时间
  * 可以直接通过of方法创建
  * 也可以通过date.atTime()或者time.atDate()来拼接日期和时间

## 2. Instant类

为了便于计算机理解，Instant类以秒为单位创建时间或日期。以Unix元年时间（UTC时区1970年1月1日午夜时分）

## 3. Duration和Period

* Duration用于处理两个同类型的时间之差，以秒或纳秒为单位，因此不支持LocalDate类型的对象作为参数。
* Period支持年、月或日为单位的时间差值计算，

## 4. 解析、处理和格式化日期

## 4.1. TemporalAdjuster(复杂日期调整类)

* 可以用于计算下个周日、下个工作日、下月最后一天等复杂的日期请求。

* 支持自定义TemporalAdjuster

## 4.2 打印输出解析日期和时间对象

* 通过DateTimeFormatter类定义格式化器，对日期和时间进行格式化。

* 支持自定义格式化器

## 4.3 支持不同的时区和历法



# 二、Optimal











# 三、lambda表达式

## 3.1 特性

* 无需声明参数类型，编译器可统一识别。
* 单个参数可以舍弃圆括号，多个参数则必须使用圆括号
* 方法体只有一条语句，可以舍弃大括号
* 方法体中唯一一条语句为返回语句，返回单个表达式，可以直接单箭头表示，省略return

## 3.2 方法引用

**语法：** **方法隶属者**  `::` **方法名**









# 四、stream流

https://mp.weixin.qq.com/s/kYJjSIFlq8x4dVBWJ1O_CQ
