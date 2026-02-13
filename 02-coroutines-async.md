# Coroutines and Asynchronous Patterns in Lua

## Basic Coroutine Usage
Coroutines are a powerful feature in Lua that allow for cooperative multitasking. They enable you to pause execution and resume it later, which is useful for tasks that can be split into smaller pieces.

### Example of a Basic Coroutine
```lua
function coFunc()
    print("Step 1")
    coroutine.yield()
    print("Step 2")
end

local co = coroutine.create(coFunc)
coroutine.resume(co) -- Step 1
coroutine.resume(co) -- Step 2
```

## Tween Animations
Tween animations can be implemented using coroutines to create smooth transitions.

### Example of a Tween Animation
```lua
function tween(start, goal, time)
    local co = coroutine.create(function()
        local elapsed = 0
        local current = start
        while elapsed < time do
            current = current + (goal - start) * (elapsed / time)
            elapsed = elapsed + 0.016  -- Simulating frame updates
            coroutine.yield(current)
        end
        return goal
    end)
    return co
end

local animation = tween(0, 1, 1)
while coroutine.status(animation) ~= "dead" do
    local value = coroutine.resume(animation)
    print(value) -- Output current value of animation
end
```

## Delay/Wait Patterns
Using coroutines, you can create delay or wait patterns that give control back to the main loop until a certain condition is fulfilled or a time period has passed.

### Delay Example
```lua
function wait(seconds)
    local start = os.clock()
    while os.clock() - start < seconds do
        coroutine.yield()
    end
end

function exampleCoroutine()
    print("Wait for 1 second")
    wait(1)
    print("1 second passed")
end

local co = coroutine.create(exampleCoroutine)
while coroutine.status(co) ~= "dead" do
    coroutine.resume(co)
end
```

## Coroutine Managers
A coroutine manager can help in organizing multiple coroutines, allowing them to be updated and resumed in a structured manner.

### Example of a Coroutine Manager
```lua
local CoroutineManager = {}
CoroutineManager.coroutines = {}

function CoroutineManager:add(co)
    table.insert(self.coroutines, co)
end

function CoroutineManager:update()
    for i = #self.coroutines, 1, -1 do
        local co = self.coroutines[i]
        if coroutine.resume(co) == false then
            table.remove(self.coroutines, i)
        end
    end
end

-- Usage Example
local manager = CoroutineManager
manager:add(coroutine.create(exampleCoroutine))
manager:update()
```

## Signal/Callback Patterns
Coroutines can be used to handle signals and callbacks in a way that maintains readability and coherence.

### Example of a Signal System with Callbacks
```lua
function signal(callback)
    local co = coroutine.create(callback)
    coroutine.resume(co)
end

signal(function()
    print("Signal received")
end)
```

## Best Practices
1. **Avoid Long Running Coroutines**: To keep the application responsive.
2. **Clean Up Resources**: Be sure to handle and release resources used by coroutines when they are done.
3. **Keep Coroutines Simple**: Each coroutine should ideally handle a single responsibility for clarity.
4. **Use Coroutine Managers**: This helps in managing multiple coroutines and avoids cluttering the main loop.  

Coroutines are an effective way to manage asynchronous tasks in Lua, providing a structured approach to concurrency that is both powerful and compact. By leveraging these patterns, developers can create responsive and animated Lua applications.