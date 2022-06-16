# data-model

## 表字段限制

- validator 
- validationLevel
    - strict 默认，对所有的插入更新应用该检查规则
    - moderate, 对插入记录以及满足规则的记录更新进行应用该检查规则
- validationAction
    - warn 对违反规则的进行日志记录，还是允许执行
    - error 不满足规则则拒绝执行

## 数据结构

- 嵌套，也称非结构化
- 结构化数据

## 运行因素

### 索引

- 每个索引至少需要 8kb 的数据空间
- 在高写入比的表中，索引是昂贵的