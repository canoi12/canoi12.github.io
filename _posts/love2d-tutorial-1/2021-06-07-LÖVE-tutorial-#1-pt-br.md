---
layout: post
title: LÖVE Básico 1 - Lua
type: Tutorial
date: 2021-06-07 19:16:40 +00:00
tags: [programming, gamedev, love2d, tutorial]
description: Pincelada sobre a linguagem de programação brasileira Lua
#image: "/apa-itu-shell/shell_evolution.png"
---

Bom, essa é um rework daquele tutorial sobre Lua, agora vai. Aqui vou dar uma pequena introdução sobre o básico:

- Tipos
- Tipos de variáveis
- Loops e Condicionais
- Tabelas
- Metatabelas

### Lua

Lua é uma linguagem de script brasileira, criada na PUC do Rio de Janeiro. A linguagem não foi criada com o foco inicial em ser usada para gamedev, mas acabou sendo adotada por vários jogos, inclusive alguns bem conhecidos como Word of Warcraft, Ragnarok, Garry's Mod, etc. Em Lua, todas as suas estruturas serão feitas através das `tabelas e metatabelas`, e é um conceito extremamente importante de se conhecer.

E se lembre, este não é um tutorial completo de Lua (se é que algo assim existe), é realmente uma pincelada sobre o assunto. Mas o ideal é vocês já terem uma certa noção para acompanharem os próximos tutoriais, então vão atrás de tutoriais, e qualquer dúvida consultem a documentação que aí sim é bem completinha [https://www.lua.org/portugues.html](https://www.lua.org/portugues.html).

#### Tipos

Lua is a dynamic typed language, but have some basic types:

Lua é uma [linguagem dinâmicamente tipada](https://robsoncastilho.com.br/2019/11/16/linguagens-estaticamente-ou-dinamicamente-tipadas), e tem alguns tipos básicos:

- Number
- String
- Boolean
- Function
- Tables
- Userdata
- Thread
- Nil

Mas como falei antes, esse não é um tutorial completo, então não irei cobrir sobre [Userdatas](https://www.lua.org/pil/28.1.html) e [Threads](https://www.lua.org/pil/9.html) por exemplo, recomendo olhar algum material mais específico.

Bom, já sobre os tipos básicos (strings, numbers, booleans e nil), eles se comportam como em qualquer outra linguagem:

- Number: 10, 20, 2021, 999
- String: "olar", "tudo bom?"
- Boolean: true, false
- Nil (equivalente a null em outras linguagens)

```lua
local str = "String" -- string
local bool = true -- boolean
local num = 10.50 -- number
local null = nil -- nil
local fun = function() end -- function
-- mesmo que
local function fun() end

local tab = {} -- table

```

Lembrando que por ser uma linguagem dinamicamente tipada, uma variável não tem tipo definido, então você pode facilmente atribuir qualquer valor a ela.

```lua
local str = "String" -- string

str = true -- agora str é uma variável booleana

```

#### Tipos de variável

Em lua existem dois tipos de variáveis, as `locais` e as `globais`, local, como o nome mesmo já diz, existe apenas em um escopo local, já variáveis globais podem ser chamadas de qualquer parte do programa.

Vejam esse exemplo e prestem atenção nas variáveis `a`, `b` e `c`:

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

Neste caso `a` existe apenas dentro da função `foo`, e não pode ser acessada fora dela. A mesma coisa para `c`, que existe somente dentro do escopo do `if`. Mas então tu deve pensar, tá e pra criar variáveis globais?

É só não colocar `local` antes de declarar.

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

Viu a diferença? Baseado nesse exemplo você consegue me dizer quais são as variáveis `locais` e `globais`?

#### Loops and Condicionais

Como a maioria das linguagens, Lua tem um condicional `if`, mas não tem um `switch`, por exemplo.

```lua
if expressão then
end
-- ou
if expressão then
else
end
-- ou
if expressão then
elseif expressão then
else
end
```

Uma `expressão` precisa ser um booleano, ou alguma expressão que retorne um booleano. Pra nos auxiliar, podemos usar os `operadores lógicos`:

- `==`: Igual
- `~=`: Diferente
- `<` : Menor
- `>` : Maior
- `<=`: Menor ou igual
- `>=`: Maior ou igual
- `and`: E lógico
- `or`: OU lógico
- `not`: Negação


```lua
local n = 20

if n < 20 then
  print('Menor que 20')
elseif n > 20 then
  print('Maior que 20')
else
  print('Igual a 20')
end
```

Em Lua, temos 3 tipos de `loops` (estruturas de repetição): `for`, `while` e `repeat..until`;

##### `while` `condição` do `código` end

Essa é a estrutura básica de um `while`, uma `condição`, assim como em uma `expressão` de um `if`, é um booleano ou uma expressão que retorne um booleano. Isso significa que enquanto nossa condição for verdadeira, nosso laço vai continuar rodando:

```lua
local n = 1
while n <= 10 do
    n = n + 1
end
```

Bastante cuidado na hora de escrever loops, você deve sempre garantir também uma maneira de sair dele, se não vamos acabar caindo nos chamados `loops infinitos`.

```lua
local n = 0
print("bora")
while n < 10 do n = n - 1 end -- fudeu
print("simbora")

```
##### `repeat` `código` `until` `condição`

`repeat..until` segue o mesmo esquemo só que ao contrário, ele repete somente até que a `condição` seja verdadeira.

```lua
local n = 1
repeat
    n = n + 1
until n == 10
```

##### `for` `inicio`,`fim`,`delta` do `código` end

Um `for` tem uma estrutura um pouco diferente da do `while`, mas para entender um pouco melhor, poderíamos traduzir isso:

```lua
local n = 1
while n <= 10 do
    print(n)
    n = n + 1
end
```

nisso:

```lua
for n=1,10,1 do
    print(n)
end
```

- `inicio` é o valor inicial do nosso `for` `n=1`
- `fim` é o valor ao qual queremos chegar `10`
- `delta` é valor que iremos adicionar à variável a cada loop `1`

Temos também dois casos especiais de loop:
`for` `chave`,`valor` in `pairs`(`tabela`) do end
`for` `index`,`valor` in `ipairs`(`tabela`) do end

Que explicaremos um pouco melhor na próxima sessão.


#### Tabelas

Tabelas em Lua são muito similares a Dicionários (`string`->`valor`) e Arrays(`número`->`valor`), a grande jogada é que nas tabelas você pode relacionar quaisquer tipos (`string`->`tabela`,`tabela`->`número`).

```lua
-- Você pode modificar valores nas tabelas de diferentes maneiras

-- Ao declarar a variável
local tab = {
  foo = "foo"
}

-- usando ponto (.)
tab.bar = "bar"

-- ou usando colchetes ([])
tab[25] = function() end 
-- Como pode ver, você nao precisa usar apenas string ou
-- apenas números como chave, pode misturar com outros tipos

-- you can even put a table inside of other
-- você pode até colocar uma tabela dentro da outra

tab.values = {
  val1 = 0,
  val2 = 0
}

for chave,valor in pairs(tab) do
  print(key, value)
end
-- print:
--- foo foo
--- bar bar
--- 25 function
--- values table

-- Lembre que:
tab.bar
-- é equivalente à
tab["bar"]
```

`pairs`, como o nome sugere, percorre a tabela pegando o par `chave`->`valor`.

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
