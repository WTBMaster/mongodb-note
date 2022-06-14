# delete

[
    { "item": "journal", "qty": 25, "size": { "h": 14, "w": 21, "uom": "cm" }, "status": "A" },
    { "item": "notebook", "qty": 50, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "P" },
    { "item": "paper", "qty": 100, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "D" },
    { "item": "planner", "qty": 75, "size": { "h": 22.85, "w": 30, "uom": "cm" }, "status": "D" },
    { "item": "postcard", "qty": 45, "size": { "h": 10, "w": 15.25, "uom": "cm" }, "status": "A" }
]

删除某表的所有记录：

    db.inventory.deleteMany({})

删除满足条件的记录：

    db.inventory.deleteMany({ status : "A" })

删除第一条满足条件的记录：

    db.inventory.deleteOne( { status: "D" } )

## 删除行为

- 即使删除表中的所有记录，也不会删除索引（删除索引数据）
- 单表操作是原子的