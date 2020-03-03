## What is

I’m studying about ECS, and tried to implement a simple one in Lua using [flecs](https://github.com/SanderMertens/flecs) as reference.

You can check it here: [https://github.com/canoi12/luna]

The lib is in early stages of development, so if you need a more complete ECS lib for LÖVE you can use:

- [knife.system](https://github.com/airstruck/knife/blob/master/readme/system.md)
- [tiny-ecs](https://github.com/bakpakin/tiny-ecs)

```lua
local luna_ecs = require("luna.ecs")

local world = luna_ecs.create_world()

world:create_component("Position", { x = 0, y = 0})
luna_ecs.create_component(world, "Velocity", { dx = 2, dy = 0})

local e = world:create_entity("Opa", "Position", "Velocity") // these functions
local ep = luna_ecs.create_entity(world, "Epa", "Position", "Velocity") // do the same

luna_ecs.entity_set(world, ep, "Position", {x = 10}) // store ids in variables and reuse

world:create_system("Move", "onLoad", { "Position", "Velocity"}, function(rows)
	local p = rows.column[1]
	local v = rows.column[2]
	for i=1,rows.count do
		p[i].x = p[i].x + v[i].dx
		p[i].y = p[i].y + v[i].dy
    print("Move: ", p[i].x, p[i].y)
  end

end)

world:create_system("Translate", "onUpdate", {"Position"}, function(rows)
	local p = rows.column[1]
	for i=1,rows.count do
		p[i].x = p[i].x + 100
    print("Translate: ", p[i].x, p[i].y)
  end
end)


for i=1,5 do
	print("loop#" .. i .. " ---------------------")
	world:update(1)
end

```

## Import the lib

```lua
local luna_ecs = require('luna.ecs')
```

# Using

---

## Creating a World

World is an object that holds all your entities, components and systems. To create a World, just call:

```lua
local world = luna_ecs.create_world()
```

After that you can call `world:luna_ecs_func(...)` or `luna_ecs.luna_ecs_func(world, ...)`, and they will be equivalent.
See an example on the next section:

## Creating an Entity

Entity are just an identification. To create an Entity:

```lua
local e = luna_ecs.create_entity(world, "Name", "Component 1", "Component 2")

-- or

local e = world:create_entity("Name", "Component 1", "Component 2")
```

## Creating a Component

Components are like Entity, but it holds data. To create a Component:

```lua
local c = luna_ecs.create_component(world, "Position", { x = 0, y = 0 })
```

## Creating a System

System are functions that have the necessary logic to act in our entities’ components. To create a System:

```lua
local s = luna_ecs.create_system(world, "Move", "onUpdate", { "Position", "Velocity"}, function(rows)
	local pos = rows.column[1]
	local vel = rows.column[2]

	for i=1,rows.count do
		pos[i].x = pos[i].x + vel[i].x
		pos[i].y = pos[i].y + vel[i].y
	end
end)

```

## Calling the loop

Simply call `world:update(1)` or `luna_ecs.update(world, 1)`
