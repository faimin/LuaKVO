# LuaKVO

`table`的属性发生变化时会执行监听回调，类似于`iOS`平台的`KVO`(键值观察)

+ 支持下标观察

+ 支持嵌套属性

#### Usage

```lua
local _class = {}

function _class:new()
    local class = {}
    class["key"] = "value"
    setmetatable(class, self)
    return self
end

local tbl = {
    person = {
        boy = {
            name = {
                n = "小明"
            }
        }
    }
}

newTable = _class:new()

newTable.key = "100"
newTable = kvo_observeForKey(newTable, "key", function(k, oldV, newV)
    print("第一", k, oldV, newV)
end)
newTable.key = 1123

function _class:addObj()
    self.key = "addObject"
end

newTable = kvo_observeForKey(newTable, "key", function(k, oldV, newV)
    print("第二", k, oldV, newV)
end)
newTable.key = "ssssss"

newTable:addObj()

kvo_removeObserveForKey(newTable, "key")
newTable.key = "看看能否监听到"


newTable.tbl = tbl
local tblProxy1 = kvo_observeForKey(newTable.tbl.person.boy.name, "n", function(k, oldV, newV)
    print("keyPath监听结果1 = ", k, oldV, newV)
end)
--如果keyPath只有一个key时,必须要替换原来的table
newTable.tbl.person.boy.name = tblProxy1
local tblProxy2 = kvo_observeForKey(newTable.tbl.person, "boy.name.n", function(k, oldV, newV)
    print("keyPath监听结果2 = ", k, oldV, newV)
end)
kvo_observeForKey(newTable.tbl, "person.boy", function(k, oldV, newV)
    print("keyPath监听结果3 = ", k, oldV, newV)
end)
newTable.tbl.person.boy.name.n = "鸿鸿"

kvo_removeObserveForKey(newTable.tbl.person.boy, "name.n")
newTable.tbl.person.boy.name.n = "删除了吗？"
newTable.tbl.person.boy = {}

return _class
```
