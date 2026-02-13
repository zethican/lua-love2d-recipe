# Metatables and Advanced Table Techniques in Lua

In Lua, metatables are a powerful feature that allows programmers to change the behavior of tables by customizing operations such as addition, subtraction, function calls, and much more. Below is a comprehensive overview of metatables and various advanced techniques when working with tables in Lua.

## 1. Operator Overloading

Operator overloading in Lua allows you to define how certain operators (like `+`, `-`, etc.) behave when they are used on your tables. To enable operator overloading, you need to set the appropriate metamethod in the metatable of the table.

```lua
local mt = {
    __add = function(t1, t2)
        return t1.value + t2.value
    end
}

local a = setmetatable({value = 10}, mt)
local b = setmetatable({value = 20}, mt)

local sum = a + b  -- This will call the __add metamethod
print(sum)  -- Output: 30
```

## 2. Default Values

To set default values for table properties, you can use metatables. This allows you to define a fallback value when a key is not found in the table.

```lua
local defaults = {x = 0, y = 0}
local mt = {
    __index = defaults
}

local point = setmetatable({}, mt)
print(point.x)  -- Output: 0 (default value)
```

## 3. Tracking Table Access

You can track when a table is accessed by using the `__index` metamethod. This can be useful for debugging or logging.

```lua
local access_count = 0
local mt = {
    __index = function(t, key)
        access_count = access_count + 1
        return rawget(t, key)
    end
}

local data = setmetatable({a = 10, b = 20}, mt)
print(data.a)  -- Output: 10
print(access_count)  -- Output: 1
```

## 4. Read-Only Tables

You can create read-only tables by implementing the `__index` metamethod to only allow access without modification.

```lua
local mt = {
    __index = function(t, key)
        return rawget(t, key)
    end,
    __newindex = function(t, key, value)
        error("Attempt to modify read-only table")
    end
}

local ro_table = setmetatable({ a = 1, b = 2 }, mt)
print(ro_table.a)  -- Output: 1
-- ro_table.a = 10  -- This will throw an error
```

## 5. Method Delegation

With metatables, you can delegate methods from one table to another. This allows for the sharing of functions between tables.

```lua
local mt = {
    __index = function(t, key)
        return MyClass[key]
    end
}

local obj = setmetatable({}, mt)
obj:someMethod()  -- This will call MyClass.someMethod()
```

## 6. Property Validation

You can enforce property validation using the `__newindex` metamethod, which lets you control what values can be set.

```lua
local mt = {
    __newindex = function(t, key, value)
        if key == "age" and (value < 0 or value > 120) then
            error("Age must be between 0 and 120")
        end
        rawset(t, key, value)
    end
}

local person = setmetatable({}, mt)
-- person.age = -5  -- This will throw an error
```

## 7. Weak References

Weak references are used when you want a table to be garbage collected, even if there are references to it elsewhere. This is particularly useful for caching.

```lua
local weak_table = setmetatable({}, { __mode = "v" })

local obj = {}
weak_table[obj] = "value"
obj = nil  -- The weak reference allows obj to be garbage collected
```

## 8. Proxy Tables

Proxy tables allow you to intercept and control access to another table, which is useful for a variety of scenarios, such as security or debugging.

```lua
local target = { a = 1, b = 2 }
local mt = {
    __index = target,
    __newindex = function(t, key, value)
        print("Setting value for key: " .. key)
        rawset(target, key, value)
    end
}

local proxy = setmetatable({}, mt)
proxy.a = 10  -- Output: Setting value for key: a
print(target.a)  -- Output: 10
```

## Conclusion

Metatables are an essential part of Lua that can greatly enhance the capabilities of tables, enabling custom behaviors, enforcing rules, and optimizing memory usage. Mastering these techniques will empower you to utilize Lua's full potential in your projects.