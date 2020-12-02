> Aggregation Pipeline Operators to spring-boot-starter-data-mongodb
mongodb聚合管道运算符转换为对应的spring-boot-starter-data-mongodb代码片段
**操作符及案例对应官方文档目录顺序和内容**,*除第一个示例外不在给出文档和结果,仅显示聚合操作*(有错误请随时指出)
[官网文档地址](https://docs.mongodb.com/manual/reference/operator/aggregation/#aggregation-pipeline-operators)
[中文文档地址](https://www.docs4dev.com/docs/zh/mongodb/v3.6/reference/reference-operator-aggregation.html)

@[TOC]( )
# mongodb聚合管道运算符
## $abs
[$abs中文文档链接](https://www.docs4dev.com/docs/zh/mongodb/v3.6/reference/reference-operator-aggregation-abs.html)
集合ratings包含以下文档：

```powershell
{ _id: 1, start: 5, end: 8 }
{ _id: 2, start: 4, end: 4 }
{ _id: 3, start: 9, end: 7 }
{ _id: 4, start: 6, end: 7 }
```
以下示例计算start和end等级之间的差异的大小：
```powershell
db.ratings.aggregate([
   {
     $project: { delta: { $abs: { $subtract: [ "$start", "$end" ] } } }
   }
])
```
该操作返回以下结果：
```powershell
{ "_id" : 1, "delta" : 3 }
{ "_id" : 2, "delta" : 0 }
{ "_id" : 3, "delta" : 2 }
{ "_id" : 4, "delta" : 1 }
```
spring-boot
```java
ProjectionOperation projection = Aggregation.project()
                    .and(ArithmeticOperators.Abs.absoluteValueOf(
                            ArithmeticOperators.Subtract.valueOf("start").subtract("end")))
                    .as("delta");
```
## $add
1. 以下聚合使用`$project`管道中的`$add`表达式来计算总费用：

```shell
db.sales.aggregate(
   [
     { $project: { item: 1, total: { $add: [ "$price", "$fee" ] } } }
   ]
)
```
```java
ProjectionOperation projection = Aggregation.project("item")
                    .and(ArithmeticOperators.Add.valueOf("price").add("fee"))
                    .as("total");
```
2. 以下汇总使用`$add`表达式，通过将3*24*60*60000毫秒(即 3 天)添加到`date`字段来计算`billing_date`

```shell
db.sales.aggregate(
   [
     { $project: { item: 1, billing_date: { $add: [ "$date", 3*24*60*60000 ] } } }
   ]
)
```
```java
ProjectionOperation projection = Aggregation.project("item")
                    .and(ArithmeticOperators.Add.valueOf("date").add(3 * 24 * 60 * 60000))
                    .as("billing_date");
```
## $addToSet
按`date`字段的日期和年份对文档进行分组，以下操作使用`$addToSet`累加器计算每个组出售的唯一商品的列表：

```shell
db.sales.aggregate(
   [
     {
       $group:
         {
           _id: { day: { $dayOfYear: "$date"}, year: { $year: "$date" } },
           itemsSold: { $addToSet: "$item" }
         }
     }
   ]
)
```

```java
这个写法暂未找到合适的springboot写法,有会的欢迎留言
```
## $allElementsTrue
以下操作使用`$allElementsTrue`运算符确定responses数组是否仅包含评估为true的值：

```java
db.survey.aggregate(
   [
     { $project: { responses: 1, isAllTrue: { $allElementsTrue: [ "$responses" ] }, _id: 0 } }
   ]
)
```
```java
ProjectionOperation projection = Aggregation.project("responses")
                    .and(SetOperators.AllElementsTrue.arrayAsSet("responses")).as("isAllTrue")
                    .andExclude(Fields.UNDERSCORE_ID);
```
## $and
以下操作使用`$and`运算符确定qty是否大于 100 *并且小于250：

```java
db.inventory.aggregate(
   [
     {
       $project:
          {
            item: 1,
            qty: 1,
            result: { $and: [ { $gt: [ "$qty", 100 ] }, { $lt: [ "$qty", 250 ] } ] }
          }
     }
   ]
)
```

```java
ProjectionOperation projection = Aggregation.project("item", "qty")
                    .and(BooleanOperators.And.and(
                            ComparisonOperators.Gt.valueOf("qty").greaterThanValue(100),
                            ComparisonOperators.Lt.valueOf("qty").lessThanValue(250)
                    )).as("result");
```
## 未完待续...
