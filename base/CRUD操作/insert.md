# 插入

下面的语句插入一条记录到 inventory 表中，如果没有指定 _id 字段，那么会自动加上 ObjectId 的 _id:
```golang
result, err := coll.InsertOne(
	context.TODO(),
	bson.D{
		{"item", "canvas"},
		{"qty", 100},
		{"tags", bson.A{"cotton"}},
		{"size", bson.D{
			{"h", 28},
			{"w", 35.5},
			{"uom", "cm"},
		}
	},
})
```
数据相当于

	 { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" }

下面的语句批量插入：
```golang
result, err := coll.InsertMany(
	context.TODO(),
	[]interface{}{
		bson.D{
			{"item", "journal"},
			{"qty", int32(25)},
			{"tags", bson.A{"blank", "red"}},
			{"size", bson.D{
				{"h", 14},
				{"w", 21},
				{"uom", "cm"},
			}},
		},
		bson.D{
			{"item", "mat"},
			{"qty", int32(25)},
			{"tags", bson.A{"gray"}},
			{"size", bson.D{
				{"h", 27.9},
				{"w", 35.5},
				{"uom", "cm"},
			}},
		},
		bson.D{
			{"item", "mousepad"},
			{"qty", 25},
			{"tags", bson.A{"gel", "blue"}},
			{"size", bson.D{
				{"h", 19},
				{"w", 22.85},
				{"uom", "cm"},
			}},
		},
	})
```

## 插入行为

- 如果表不存在，插入会自动创建
- 记录需要唯一的 _id 字段作为主键，如果未主动提供，那么系统会以ObjectId自动生成
- 对单一表的写是原子的