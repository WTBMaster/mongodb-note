# 查询 query

https://www.mongodb.com/docs/manual/tutorial/query-documents/

数据

        [
                { "item": "journal", "qty": 25, "size": { "h": 14, "w": 21, "uom": "cm" }, "status": "A" },
                { "item": "notebook", "qty": 50, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "A" },
                { "item": "paper", "qty": 100, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "D" },
                { "item": "planner", "qty": 75, "size": { "h": 22.85, "w": 30, "uom": "cm" }, "status": "D" },
                { "item": "postcard", "qty": 45, "size": { "h": 10, "w": 15.25, "uom": "cm" }, "status": "A" }
        ]

下面的查询语句获取在表 inventory 中所有 status 为 A 或者 D 的记录：
        
        cursor, err := coll.Find(
                context.TODO(),
	        bson.D{{"status", bson.D{{"$in", bson.A{"A", "D"}}}}})

下面的查询语句获取在表 inventory 中所有 status 等于 A 并且 qty 小于 30:
        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"status", "A"},
                        {"qty", bson.D{{"$lt", 30}}},
	})

下面的查询语句获取 status 等于 A 或者 qty 小于 30 的记录：
        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"$or",
                                bson.A{
                                        bson.D{{"status", "A"}},
                                        bson.D{{"qty", bson.D{{"$lt", 30}}}},
                                }},
	})



## Match an Array

        [
                { "item": "journal", "qty": 25, "tags": ["blank", "red"], "dim_cm": [ 14, 21 ] },
                { "item": "notebook", "qty": 50, "tags": ["red", "blank"], "dim_cm": [ 14, 21 ] },
                { "item": "paper", "qty": 100, "tags": ["red", "blank", "plain"], "dim_cm": [ 14, 21 ] },
                { "item": "planner", "qty": 75, "tags": ["blank", "red"], "dim_cm": [ 22.85, 30 ] },
                { "item": "postcard", "qty": 45, "tags": ["blue"], "dim_cm": [ 10, 15.25 ] }
        ]


下面的查询语句获取 tags 只有2个元素，并且按顺序为 red blank 值的所有记录：

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{{"tags", bson.A{"red", "blank"}}},
        )

如果只关心记录是否包含 red blank, 而不关心顺序、是否还有其他值，可以这样查：

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"tags", bson.D{{"$all", bson.A{"red", "blank"}}}},
	})

下面的查询语句获取 tags 数组包含 red 的记录：

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"tags", "red"},
	})

下面的查询语句获取 dim_cm 至少含有一个元素大于 25 的所有记录：
        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"dim_cm", bson.D{
                                {"$gt", 25},
                        }},
	})

下面的查询语句获取 dim_cm 字段中，无论是一个同时满足，还是分别满足大于 15， 小于 20 的所有记录：

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"dim_cm", bson.D{
                                {"$gt", 15},
                                {"$lt", 20},
                        }},
	})

可以使用 $elemMatch 来指定一个元素同时满足条件，例如下面的查询语句返回 dim_cm 字段至少有一个元素同时满足大于 22, 小于 30 的所有记录：

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"dim_cm", bson.D{
                                {"$elemMatch", bson.D{
                                        {"$gt", 22},
                                        {"$lt", 30},
                                }},
                        }},
	})

下面的查询语句获取 dim_cm 数组的第2个元素大于 25 的所有记录：
        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"dim_cm.1", bson.D{
                                {"$gt", 25},
                        }},
	})

通过 $size 来查询 tags 数组的长度：

        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"tags", bson.D{
                                {"$size", 3},
                        }},
	})

-----------------------------------------------------------------------------------------------------------------

        [ 
                { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
                { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
                { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
                { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
                { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
        ]

下面的查询需要匹配顺序：【个数呢？】


        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"instock", bson.D{
                                {"warehouse", "A"},
                                {"qty", 5},
                        }},
	})

下面的查询语句获取 instock 的第一个元素 qty 不大于 20 的所有记录：


        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"instock.0.qty", bson.D{
                                {"$lte", 20},
                        }},
	})

下面的查询语句获取 instock 中元素同时满足还是分别满足 qty = 5 并且 warehouse = A 的语句

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"instock.qty", 5},
                        {"instock.warehouse", "A"},
	})

同样的，可以通过 $elemMatch 修改该行为为一个元素同时满足才行：


        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"instock", bson.D{
                                {"$elemMatch", bson.D{
                                        {"qty", bson.D{
                                                {"$gt", 10},
                                                {"$lte", 20},
                                        }},
                                }},
                        }},
	})


## 控制返回字段

        [
                { "item": "journal", "status": "A", "size": { "h": 14, "w": 21, "uom": "cm" }, "instock": [ { "warehouse": "A", "qty": 5 } ] },
                { "item": "notebook", "status": "A", "size": { "h": 8.5, "w": 11, "uom": "in" }, "instock": [ { "warehouse": "C", "qty": 5 } ] },
                { "item": "paper", "status": "D", "size": { "h": 8.5, "w": 11, "uom": "in" }, "instock": [ { "warehouse": "A", "qty": 60 } ] },
                { "item": "planner", "status": "D", "size": { "h": 22.85, "w": 30, "uom": "cm" }, "instock": [ { "warehouse": "A", "qty": 40 } ] },
                { "item": "postcard", "status": "A", "size": { "h": 10, "w": 15.25, "uom": "cm" }, "instock": [ { "warehouse": "B", "qty": 15 }, { "warehouse": "C", "qty": 35 } ] }
        ]


        projection := bson.D{
                {"item", 1},
                {"status", 1},
                {"_id", 0},             // 默认传，设置为0，可以排除指定字段
                {"size.uom", 1},        // 嵌套字段同样可以设置
                {"instock", bson.D{
                        {"$slice", -1}, // 返回 instock 数组的最后一个元素
                }},
        }

        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"status", "A"},
                },
                options.Find().SetProjection(projection),
        )

## Query for Null or Missing Fields

        [
                { "_id": 1, "item": null },
                { "_id": 2 }
        ]

下面的语句查询 item 为 null 或者缺少该字段的所有记录：


        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"item", nil},
	})

下面的语句只查询 item 为 null 的所有记录：

        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"item", bson.D{
                                {"$type", 10},
                        }},
	})

下面的语句只查询不包含 item 字段记录：

        
        cursor, err := coll.Find(
                context.TODO(),
                bson.D{
                        {"item", bson.D{
                                {"$exists", false},
                        }},
	})

## 其他API

要看下具体语言的实现。

### findAndModify
```
db.collection.findAndModify({
    query: <document>,
    sort: <document>,
    remove: <boolean>,
    update: <document or aggregation pipeline>, // Changed in MongoDB 4.2
    new: <boolean>,
    fields: <document>,
    upsert: <boolean>,
    bypassDocumentValidation: <boolean>,
    writeConcern: <document>,
    collation: <document>,
    arrayFilters: [ <filterdocument1>, ... ],
    let: <document> // Added in MongoDB 5.0
});
```

- new: 是否返回最新的记录，第一条记录
- upsert: 不存在则插入，下同

### findOneAndUpdate
```
db.collection.findOneAndUpdate(
   <filter>,
   <update document or aggregation pipeline>, // Changed in MongoDB 4.2
   {
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
     upsert: <boolean>,
     returnDocument: <string>,
     returnNewDocument: <boolean>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ]
   }
)
```

### findOneAndReplace
```
db.collection.findOneAndReplace(
   <filter>,
   <replacement>,
   {
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
     upsert: <boolean>,
     returnDocument: <string>,
     returnNewDocument: <boolean>,
     collation: <document>
   }
)
```