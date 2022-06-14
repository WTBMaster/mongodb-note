# update

  [
    { "item": "canvas", "qty": 100, "size": { "h": 28, "w": 35.5, "uom": "cm" }, "status": "A" },
    { "item": "journal", "qty": 25, "size": { "h": 14, "w": 21, "uom": "cm" }, "status": "A" },
    { "item": "mat", "qty": 85, "size": { "h": 27.9, "w": 35.5, "uom": "cm" }, "status": "A" },
    { "item": "mousepad", "qty": 25, "size": { "h": 19, "w": 22.85, "uom": "cm" }, "status": "P" },
    { "item": "notebook", "qty": 50, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "P" },
    { "item": "paper", "qty": 100, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "D" },
    { "item": "planner", "qty": 75, "size": { "h": 22.85, "w": 30, "uom": "cm" }, "status": "D" },
    { "item": "postcard", "qty": 45, "size": { "h": 10, "w": 15.25, "uom": "cm" }, "status": "A" },
    { "item": "sketchbook", "qty": 80, "size": { "h": 14, "w": 21, "uom": "cm" }, "status": "A" },
    { "item": "sketch pad", "qty": 95, "size": { "h": 22.85, "w": 30.5, "uom": "cm" }, "status": "A" }
  ]

- $set 如果该字段不存在，则会创建

下面的语句更新满足 item = "paper" 的第一条记录：
```golang
result, err := coll.UpdateOne(
	context.TODO(),
	bson.D{
		{"item", "paper"},
	},
	bson.D{
		{"$set", bson.D{
			{"size.uom", "cm"},
			{"status", "P"},
		}},
		{"$currentDate", bson.D{
			{"lastModified", true},
		}},
	},
)
```
- $set 更新 size.uom = cm, status = p
- $currentDate 更新 lastModified，如果该字段不存在，那么会创建

一次更新多条记录：
```golang
result, err := coll.UpdateMany(
	context.TODO(),
	bson.D{
		{"qty", bson.D{
			{"$lt", 50},
		}},
	},
	bson.D{
		{"$set", bson.D{
			{"size.uom", "cm"},
			{"status", "P"},
		}},
		{"$currentDate", bson.D{
			{"lastModified", true},
		}},
	},
)
```

ReplaceOne:
```golang
result, err := coll.ReplaceOne(
	context.TODO(),
	bson.D{
		{"item", "paper"},
	},
	bson.D{
		{"item", "paper"},
		{"instock", bson.A{
			bson.D{
				{"warehouse", "A"},
				{"qty", 60},
			},
			bson.D{
				{"warehouse", "B"},
				{"qty", 40},
			},
		}},
	},
)
```

## 增加列，删除列

    // 增加列
    db.people.updateMany(
      { },
      { $set: { join_date: new Date() } }
    )

    // 删除列
    db.people.updateMany(
      { },
      { $unset: { "join_date": "" } }
    )