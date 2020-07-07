---
layout: post
title: LÖVE Basics 1 - Lua basics
type: Tutorial
date: 2020-06-25 00:11:40 +00:00
modified: 2020-06-25 00:11:40 +07:00
tags: [programming, gamedev, love2d, tutorial]
description: LÖVE Basics 1 - Lua basics
#image: "/apa-itu-shell/shell_evolution.png"
---

Thats the first tutorial about LÖVE series i plan to write, in this tutorial i will give a brief introduction about the Lua basics

- Lua types
- Variable scope
- Loops and Conditionals
- Tables
- Metatables (with a pseudo-OOP example)

YOU DON'T NEED TO IMPLEMENT DE OOP PART YOURSELF!! We'll use a lib to that, i just want to explain some concepts

### Lua

Lua is a scripting Brazilian language, created in PUC of Rio de Janeiro. The language was not created with gamedev in mind, but was vastly used in many games like Word of Warcraft, Ragnarok, Garry's Mod, etc. In Lua, all your game structure will be mounted around `tables/metatables`, thats a very important concept to understand, for example, Lua don't have OOP, a sort of "Pseudo-OOP" with it

And remember, thats not a Lua tutorial, i'll only show come basic aspects of the program, if you don't know anything abou Lua, i recommend learn about before.

#### Types

Lua is a dynamic typed language, but have some basic types:

- Number
- String
- Boolean
- Function
- Tables
- Userdata
- Thread
- Nil

Userdata you don't need to understand for now, they are types created on C side. I'll not cover about threads neither.

I think numbers, strings, booleans, nils and functions don't need explications too, it works like in any other language. As Lua is dynamic typed, you don't explicit declare a type, but you just assign a value to a variable.

```lua
local str = "String" -- string
local bool = true -- boolean
local num = 10.50 -- number
local fun = function() end -- function
local null = nil -- nil
-- same as
local function fun() end

local tab = {} -- table

```

Remember that any variable can be reassigned to other types

```lua
local str = "String" -- string

str = true -- now str is a boolean

```

#### Variable scope

In Lua there are 2 types of variables, `local` and `global`, local, as the name says, only exists on the local scope, and global can be called in any part of the program. Example:

```lua
function foo()
  local a = 10
  print(a)
end

foo() -- print 10
print(a) -- print 'nil'

local b = 10
if b >= 10 then
  local c = 20
end
print(b) -- print 10
print(c) -- print 'nil'
```

In this case, `a` just exists in the function `foo`, and can't be accessed outside of it. Same for `c`, it only exists in the `if` scope. Okay, but then how to declare a global variable?

You just not put `local` before declare:

```lua
function foo()
  local b = 40
end

function bar()
  a = 20
end

foo()
bar()

print(a) -- print 20
print(b) -- print 'nil'

local c = 20
if c > 10 then
  d = 25
end
print(c) -- print 20
print(d) -- print 25
```

`a` is a global variable, and now you can access it from any part of the program, same for `c`. Lua makes that putting our variable in a table called `_G`. Before talk abou table, let's see briefly about loops and conditionals

#### Loops and Conditionals

Lua, like most languages has an `if` conditinal, but it don't have `switch`. The structure is:

```lua
if expression then
end

-- or

if expression then
else
end

-- or

if expression then
elseif expression then
else
end
```

An `expression` need to be a boolean or some expression that returns a boolean, for that we can use some operators:

- `==`: Equal
- `~=`: Not equal
- `<` : Less than
- `>` : Greater than
- `<=`: Less than or equal
- `>=`: Greater than or equal
- `and`: Logical and
- `or`: Logical or
- `not`: Boolean not


```lua
local n = 20

if n < 20 then
  print('Less than 20')
elseif n > 20 then
  print('Greater than 20')
else
  print('Equal 20')
end
```

In Lua, we have 3 types of loops: `for`, `while` and `repeat..until`:

```lua
while condition do
end

repeat
until condition

for from,to,step do
end

for key,value in pairs(table)/ipairs(table) do
end
```

`condition` like in `if` is a boolean or a expression that returns a boolean, so:

```lua
local n = 0

while n < 10 do
  n = n + 1
end

n = 0

repeat
  n = n + 1
until n > 10
```

Notice the difference between `while` and `repeat..until`, `while` repeat while the conditions is true, and `repeat..until` repeat while the condition is false.

And last but not less important, there is `for` loop:

- `from`: is the start value
- `to`: is the last value
- `step`: is how much we step every iteration

```lua
for i=1,5 do
  print(i)
end

-- print
-- 1
-- 2
-- 3
-- 4
-- 5

for i=1,5,2 do -- its like i = i + 2
  print(i)
end

-- print
-- 1
-- 3
-- 5

-- step can be negative too
for i=5,1,-1 do
  print(i)
end

-- 5
-- 4
-- 3
-- 2
-- 1

--- and the principal use of for loop, we can iterate over tables

for key,value in pairs(table) do
end

for i,val in ipairs(table) do
end
```

But before explain the last cases, let's see about tables

#### Tables

Tables in Lua are very similar with JavaScript objects (relation: key -> value) and arrays (relation: number -> value), but in Lua both are just tables (relation: lua_value -> lua_value) as you can even use functions as keys, but some functions just work for tables when implement as arrays, for example.

Let's do some code

```lua
-- You can assign values to tables in different ways

-- When creating the table
local tab = {
  foo = "foo"
}

-- using dot (.)
tab.bar = "bar"

-- or using brackets ([])
tab[25] = function() end 
-- As you can see, you don't need to use just string or 
-- just numbers as a key, you can mix with other types

-- you can even put a table inside of other

tab.values = {
  val1 = 0,
  val2 = 0
}

for key,value in pairs(tab) do
  print(key, value)
end
-- print:
--- foo foo
--- bar bar
--- 25 function
--- values table

-- Remember:
tab.bar
-- is equivalent to
tab["bar"]
```

`pairs` as the names says, iterate over our table getting the pair `key` `value`

Okay, now let's make other thing:

```lua
-- When we declare a table just with numeric values
-- it works as an array, and you can use some functions

local tab = {}

-- for insert values in a array table, 
-- it's common use a Lua function called table.insert
table.insert(tab, 20)
table.insert(tab, 25)

-- you can use '#' before your array table to get his size
print(#tab) -- print 2

for index,value in ipairs(tab) do
  print(index, value)
end
```

You can use `pairs` to iterate over an array table too, but if you expect that the loops only works with an array table, use `ipairs`.

If you run the above code you will notice that it will print:

```
1 20
2 25
```

In Lua, the array index starts in 1, if you try to access `tab[0]` it will return a nil value

Other important concept in tables is the operator `:`, check the code.

```lua
local player = {
  x = 0,
  y = 0
}

player.move = function(tab, x, y)
  tab.x = tab.x + x
  tab.y = tab.y + y
end

player.move(player, 10, 0)
```

With the operator `:` we can just call

```lua
player:move(10, 0)
```

It works the same way, when using `:` it passes the table calling the function as the first parameter of the function

You can use it when declaring the function too, and the first argument of the function will be called `self`

```lua
local player = {
  x = 0,
  y = 0
}

function player.move(self, x, y)
  self.x = self.x + x
  self.y = self.y + y
end

-- is equivalent to

function player:move(x, y)
  self.x = self.x + x
  self.y = self.y + y
end
```

So, using that, we can make a simple player class:

```lua
local Player = {
  x = 0,
  y = 0
}

function Player:init(x, y)
  self.x = x
  self.y = y
end

function Player:update(dt)
  -- update the player
end

function Player:draw()
  -- draw the player
end
```

Okay, but we need to create a table to every object in my game? Let's increment our player class using something called `metatables`

#### Metatables

Metatables are simple Lua tables but with cool functions, metatables alone do nothing until you assign them to other table, let me show you a example of a simple Vector class:

```lua
local Vector = {
  x = 0,
  y = 0
}

local vec_mt = {}

setmetatable(Vector, vec_mt)
```

In this example `Vector` is our normal table, and `vec_mt` acts like our metatable, we assign that using the function `setmetatable(table, metatable)`, and we can define specific functions in our metatable to do cool stuf:

- `__add`: is called when we sum our table with another value `local sum = table + other`
- `__sub`: is called when we subtract our table with another value `local sub = table - other`
- `__mul`: is called when we multiply our table with another value  `local mul = table * other`
- `__div`: is called when we divide our table with another value  `local sum = table / other`

```lua
local Vector = {
  x = 20,
  y = 90
}

local vec_mt = {
  __add = function(v1, v2)
    local x = v1.x + v2.x
    local y = v1.y + v2.y
    return {x = x, y = y}
  end
}

setmetatable(Vector, vec_mt)

local sum = Vector + Vector
print(sum.x, sum.y) -- will print 40 180

-- Notice you can't do
local sum2 = sum + sum

-- because 'sum' don't have a metatable, it's just a normal table
```

There are many other functions, they are called metatable events, and you can check here [http://lua-users.org/wiki/MetatableEvents](http://lua-users.org/wiki/MetatableEvents)

But there are two in special that are really helpful to create a pseudo OOP, `__index` and `__call`, lets check other example:

```lua
-- Let's create a factory to create an Animal object

function createAnimal(legs)
  local animal = {
    legs = legs,
    printLegs = function(self)
      print(self.legs)
    end
  }
  setmetatable(animal, {})
  return animal
end

local dog = createAnimal(4)
local snake = createAnimal(0)

dog:printLegs()
snake:printLegs()
```

And if we want to create a factory for our snakes?

```lua
function createAnimal(legs)
  local animal = {
    legs = legs,
    printLegs = function(self)
      print(self.legs)
    end
  }
  setmetatable(animal, {})
  return animal
end

function createSnake()
  local snake = createAnimal(0)
  return snake
end

local dog = createAnimal(4)
local snake = createSnake()

dog:printLegs()
snake:printLegs()
```

Easy, right? But there is a better way to do that using the `__index` event.

A `__index` is another table to find our functions/attributes, if a given function don't exists in the current table, it will search in the another one. Example:

```lua
local someTable = {}
function someTable:foo()
  print('batata doce')
end

local otherTable = {}
setmetatable(otherTable, { __index = someTable })

otherTable:foo() -- print batata doce
```

Notice that `otherTable` don't implement `foo`, but as we give it a metatable with `__index` pointing to `someTable`, it check if it implements the function

Let's change a bit our class structure:

```lua
local Animal = {}
Animal.__index = Animal

-- our factory becomes
function Animal:new(legs)
  local o = setmetatable({}, {__index = Animal})
  o.legs = legs
  return o
end

function Animal:printLegs()
  print(self.legs)
end

local dog = Animal:new(4)
local snake = Animal:new(0)

dog:printLegs()
snake:printLegs()
```

Notice the code works the same way, and we aren't create a table with all the contents every time, but just point to other with all functions we want. And if we add new functions to the `Animal` class, it reflects on our objects

```lua
function Animal:hasLegs()
  return self.legs > 0
end

print(dog:hasLegs())
print(snake:hasLegs())
```

And now, if we want to make a snake class? And pass different arguments in the constructor?

We can create a function only for the constructor and change the `new` function a bit

```lua
function Animal:new(...) -- pass variable arguments
  -- will explain '__index = self' later 
  local o = setmetatable({}, { __index = self })
  o:constructor(...) -- call the constructor passing our arguments
  return o 
end

function Animal:constructor(legs)
  self.legs = legs
end
```

To say that our `Snake` class inherits from `Animal` we do

```lua
local Snake = setmetatable({}, { __index = Animal })
-- or, as our Animal table has an __index attribute pointing to himself
-- we can just do
local Snake = setmetatable({}, Animal)
```

Let's create another attribute to our `Snake` class and override the constructor

```lua
function Snake:constructor(venomous)
  Animal.constructor(self, 0) -- call Animal constructor
  self.venomous = venomous
end

function Snake:isVenomous()
  return self.venomous
end
```

Now the code should look like this

```lua
local Animal = {}
Animal.__index = Animal

function Animal:new(...)
  local o = setmetatable({}, {__index = self})
  o:constructor(...)
  return o
end

function Animal:constructor(legs)
  self.legs = legs
end

function Animal:printLegs()
  print(self.legs)
end

local Snake = {}
setmetatable(Snake, Animal)

function Snake:constructor(venomous)
  Animal.constructor(self, 0)
  self.venomous = venomous
end

function Snake:isVenomous()
  return self.venomous
end

local snake = Snake:new(false)
local snake2 = Snake:new(true)

snake:printLegs() -- print 0
print(snake:isVenomous()) -- print false
print(snake2:isVenomous()) -- print true
```

We need to use 
```lua 
local o = setmetatable({}, {__index = self}) 
``` 
cause if we keep `__index = Animal`, all snake objects will point just to `Animal`, and will not inherit any of the funcions from the `Snake` class. If we don't do that, we need to create a `new` function to our Snake too

```lua
function Snake:new(...)
  local o = setmetatable({}, {__index = Snake})
  o:constructor(...)
  return o
end
```

But thats a problem, because we need to create a `new` function for all our subsequent classes that inherits from `Snake` or `Animal`

So making:

```lua
function Animal:new(...)
  local o = setmetatable({}, {__index = self})
  o:constructor(...)
  return o
end

-- when we call
local snake = Snake:new(false)

-- is the same as
Animal.new(Snake, false)

function Animal.new(Snake, false)
  local o = setmetatable({}, { __index = Snake })
  o:constructor(false)
  return o
end
```

And to finish, let's talk about `__call`, fortunately is very very simpler than `__index`. `__call` is a function called when we call our table as a function, eg. `myTable()`

```lua
local tab = {
  x = 10
}
local mt = {
  -- remember to pass 'self' as the first argument
  __call = function(self, num)
    return num * self.x
  end
}

setmetatable(tab, mt)

local n = tab(5)
print(n) -- print 50
```

Lets implement a simple OOP class lib:

```lua
local Class = {}
Class.__index = Class


function Class:new(...)
  local o = setmetatable({}, {__index = self})
  o:constructor(...)
  return o
end

function Class:extend()
  local o = setmetatable({}, {__index = self, __call = Class.new })
  return o
end

function Class:constructor()
end

-----------------------------------

local GameObject = Class:extend()

function GameObject:constructor(x, y)
  print("GAME OBJECT CONSTRUCTOR")
  self.x = x or 0
  self.y = y or 0
end

function GameObject:move(x, y)
  self.x = self.x + x
  self.y = self.y + y
  print("new pos:", self.x, self.y)
end

local Player = GameObject:extend()

function Player:constructor(x, y)
  GameObject.constructor(self, x, y)
  print("PLAYER CONSTRUCTOR")
end

local Enemy = GameObject:extend()

function Enemy:constructor(x, y)
  GameObject.constructor(self, x, y)
  print("ENEMY CONSTRUCTOR")
end

local player = Player(20, 10)
player:move(20, 40)


local enemy = Enemy(5, 45)
enemy:move(10, 90)
```

And there is! As i said before, that first tutorial is just to show some Lua basics, but if you don't know absolutely nothing about the language, i recommend you learn before continue using a more complete material.

In the next tutorial, we'll in fact start with LÖVE.
