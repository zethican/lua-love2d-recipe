# Metatable Magic and Advanced Table Techniques

## Overview
Metatables are a powerful feature of Lua that allow you to define custom behaviors for tables. This document explores advanced techniques involving metatables, including operator overloading, default values, tracking table access, read-only tables, validation, weak references, and proxy patterns.

## 1. Operator Overloading
Operator overloading allows you to define how an operator behaves when applied to table instances. You can create custom behaviors for operations such as addition, subtraction, and even concatenation. Here’s how you can overload the addition operator:

```lua
local Vector = {}
Vector.__index = Vector

function Vector.new(x, y)
    return setmetatable({x = x, y = y}, Vector)
end

function Vector.__add(a, b)
    return Vector.new(a.x + b.x, a.y + b.y)
end

local v1 = Vector.new(1, 2)
local v2 = Vector.new(3, 4)
local v3 = v1 + v2  -- uses __add
print(v3.x, v3.y)  -- Outputs: 4 6
```

## 2. Default Values
Metatables can also be used to set default values for table entries. By using the `__index` metamethod, you can provide default values when accessing undefined keys:

```lua
local defaults = {x = 0, y = 0}
local obj = setmetatable({}, {__index = defaults})

print(obj.x)  -- Outputs: 0
print(obj.y)  -- Outputs: 0
obj.x = 10
print(obj.x)  -- Outputs: 10
```

## 3. Tracking Table Access
You can track access to table fields by defining custom behaviors in the `__index` and `__newindex` metamethods:

```lua
local t = setmetatable({}, {__index = function(tbl, key)
    print("Accessed key: " .. key)
    return nil
end,
$newindex = function(tbl, key, value)
    print("Setting key: " .. key .. " to " .. value)
    rawset(tbl, key, value)
end
})

t.a = 10  -- Setting key: a to 10
print(t.a)  -- Accessed key: a
                -- Outputs: 10
```

## 4. Read-Only Tables
Creating a read-only table can prevent modification of its contents:

```lua
local function readonly(t)
    return setmetatable({}, {__index = t,  __newindex = function() error("Attempt to modify a read-only table") end})
end

local t = readonly({x = 10})
print(t.x)  -- Outputs: 10
-- t.x = 20  -- This will throw an error
```

## 5. Validation
You can use metatables to enforce value validation on table entries:

```lua
local function newPerson()
    return setmetatable({}, {__newindex = function(t, key, value)
        if key == "age" and (not tonumber(value) or value < 0) then
            error("Invalid age value")
        end
        rawset(t, key, value)
    end})
end

local person = newPerson()
-- person.age = -1  -- This will throw an error
person.age = 25  -- Valid
```

## 6. Weak References
Weak references allow for garbage collection without preventing the referenced values from being collected:

```lua
local t = {}  -- normal table
setmetatable(t, {__mode = "v"})  -- weak values

local key = {}  -- key to reference
t[key] = "value"
key = nil  -- The value can now be garbage collected
```

## 7. Proxy Patterns
You can implement a proxy pattern to encapsulate access to a table:

```lua
local function proxy(t)
    return setmetatable({}, {
        __index = t,
        __newindex = function(_, key, value)
            print("Setting key: " .. key)
            rawset(t, key, value)
        end,
        __metatable = "This is a readonly metatable"
    })
end

local t = {a = 1}
local p = proxy(t)
print(p.a)  -- Outputs: 1
p.a = 2  -- Setting key: a
print(t.a)  -- Outputs: 2
```

## Conclusion
Metatables are a fundamental part of Lua that allow developers to create powerful abstractions and behaviors for tables. By utilizing advanced techniques like operator overloading, default values, and validation, you can build complex systems that are efficient and easy to maintain.