---
layout: post
title: LÖVE Básico 1 - Lua
type: Tutorial
date: 2021-06-07 19:16:40 +00:00
tags: [programming, gamedev, love2d, tutorial]
description: Pincelada sobre a linguagem de programação brasileira Lua
#image: "/apa-itu-shell/shell_evolution.png"
---

Bom, esse texto é uma tradução daquele tutorial sobre Lua com algumas adaptações e modificações, agora vai! Aqui vou dar uma pequena introdução sobre o básico:

- Tipos
- Tipos de variáveis
- Laços e Condicionais
- Tabelas
- Metatabelas

### Lua

Lua é uma linguagem de script brasileira, criada na PUC do Rio de Janeiro. A linguagem não foi criada com o foco inicial em ser usada para gamedev, mas acabou sendo adotada por vários jogos, inclusive alguns bem conhecidos como World of Warcraft, Ragnarok, Garry's Mod, etc. Em Lua, todas as suas estruturas serão feitas através das `tabelas e metatabelas`, por isso este é um conceito extremamente essencial de se aprender.

E se lembre, este não é um tutorial completo de Lua, é realmente uma pincelada sobre o assunto. O ideal é você já ter uma certa noção para acompanhar os próximos tutoriais, então vá atrás de tutoriais, e qualquer dúvida consulte a documentação que aí sim é bem completo [https://www.lua.org/portugues.html](https://www.lua.org/portugues.html).

#### Tipos

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

Bom, já sobre os tipos básicos (Strings, Numbers, Booleans e Nil), eles se comportam como em qualquer outra linguagem:

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

print(type(tab)) -- 'table'
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

foo() -- '10'
print(a) -- 'nil'

local b = 10
if b >= 10 then
  local c = 20
end
print(b) -- '10'
print(c) -- 'nil'
```

Neste caso `a` existe apenas dentro da função `foo`, e não pode ser acessada fora dela. A mesma coisa para `c`, que existe somente dentro do escopo do `if`. Mas então tu deve pensar, tá, e pra criar variáveis globais?

É só não colocar `local` antes de declarar:

```lua
function foo()
  local b = 40
end

function bar()
  a = 20
end

foo()
bar()

print(a) -- '20'
print(b) -- 'nil'

local c = 20
if c > 10 then
  d = 25
end
print(c) -- '20'
print(d) -- '25'
```

Viu a diferença? Baseado nesse exemplo você consegue me dizer quais são as variáveis `locais` e `globais`?

#### Laços and Condicionais

Como a maioria das linguagens, Lua tem um condicional `if`:

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

`Laços` são estruturas de repetição, em lua temos 3 deles: `for`, `while` e `repeat..until`;

- ### `while` condição `do` código `end`

Essa é a estrutura básica de um `while`, uma `condição`, assim como em uma `expressão` de um `if`, é um booleano ou uma expressão que retorne um booleano. Isso significa que enquanto nossa condição for verdadeira, nosso laço vai continuar se repetindo:

```lua
local n = 1
while n <= 10 do
    n = n + 1
    print('n:', n)
end
```

Bastante cuidado na hora de escrever laços, você deve sempre garantir também uma maneira de sair dele, se não vamos acabar caindo nos chamados `laços infinitos`.

```lua
local n = 0
print("bora")
while n < 10 do n = n - 1 end -- já era fi
print("simbora")

```
- ### `repeat` código `until` condição

`repeat..until` é o contrário do `while` esse laço se repete ATÉ QUE a `condição` seja verdadeira.

```lua
local n = 1
repeat
    n = n + 1
until n == 10
```

- ### `for` inicio,fim,delta `do` código `end`

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

- `inicio` é o valor inicial do nosso `for` -- `n=1`
- `fim` é o valor ao qual queremos chegar -- `10`
- `delta` é valor que iremos adicionar à variável a cada repetição -- `1`

Temos também dois casos especiais de `laços`:
- `for` chave,valor `in` `pairs`(tabela) `do` `end`
- `for` index,valor `in` `ipairs`(tabela) `do` `end`

Que explicaremos um pouco melhor na próxima sessão.

### [Tabelas](http://lua-users.org/wiki/TablesTutorial)

Agora chegamos no ponto principal, as `tabelas`:

```lua
local pessoa = {}
pessoa.nome = "Fulano"
pessoa.idade = 2021
pessoa["peso"] = 92
pessoa.pular = function()
  print('opa')
end
-- ou
local pessoa = {
    nome = "Fulano",
    idade = 2021,
    peso = 92,
    pular = function()
      print('opa')
    end
}
```

Como deu pra ver, tabelas são estruturas similares a `objetos` em outras linguagens, Nós temos uma relação {`chave`->`valor`}. A grande diferença é que essa `chave` não precisa ser necessariamente uma string, também podem ser valores de outros tipos como números, funções e até mesmo outras tabelas.

```lua
local A = {}
local B = {}

A[B] = B
B[A] = A

print(A[B], B[A])
```

Outra forma de se construir tabelas é utilizando somente números como chave. Com isso nós vamos ter um comportamento parecido com `arrays` em outras linguagens. Pra inserir os objetos nós utilizamos uma função que faz parte de lib padrão Lua, a `table.insert`.

```lua
local t = {}
table.insert(t, 10)
table.insert(t, 20)
table.insert(t, "trinta")
```

Ela insere o objeto no final da nossa tabela. E como eu disse, ao construir uma tabela desse jeito, seu comportamento se assemelha ao de um array, então pra acessar um determinado índice:

```lua
print(t[2], t[3])
```

Ah, detalhe importante, diferente de outras linguagens como `C` por exemplo, onde o índice do array começa em 0, em Lua o índice sempre começa em 1.

```lua
local t = {}
table.insert(t, 10)
print(t[1])
```

Lembra dos dois tipos de `for` que eu comentei antes?

- `for` `chave`,`valor` in `pairs`(`tabela`) do end
- `for` `index`,`valor` in `ipairs`(`tabela`) do end

E é exatamente com as tabelas que iremos usar elas. No caso da função `ipairs`, ela funciona somente com tabelas criadas do jeito que falei anteriormente, como um array.

```lua
local t = {}
t.viji = true
t["bora"] = "vamo"
t[t] = "cuidado"

for k,v in pairs(t) do
    print(k, v)
end
--- viji true
--- bora vamo
--- 0x.. cuidado
```

```lua
local t = {}
table.insert(t, 25)
table.insert(t, 28)
table.insert(t, 52)
for i,v in ipairs(t) do
    print (i, v)
end
--- 1 25
--- 2 28
--- 3 52
```

Também tem outras coisinhas que são diferentes dependendo de como você constrói sua tabela. Usando `#` você consegue pegar o tamanho da tabela, por exemplo.

```lua
local t = {}
table.insert(t, 90)
table.insert(t, 95)
print(#t) -- 2
```

Mas só funciona com "arrays"..

```lua
local t = {}
t.nome = "João"
print(#t) -- 0
```

Enfim, recomendo dar uma olhada melhor no assunto.

Outro operador extremamente importante é o `:`, veja o exemplo:

```lua
local gato = { vidas = 7 }

gato.morre = function(g)
    if g.vidas > 0 then
        print("te liga")
        g.vidas = g.vidas - 1
    else
        print("casa caiu")
    end
end

gato.morre(gato)
```

Utilizando o operador `:` para chamar a função, podemos escrever somente:

```lua
gato:morre()
```

Percebeu o que acontece? Basicamente com o operador `:` nós temos um atalho pra utilizar funções onde o primeiro argumento é a própria tabela.
E isso não se restringe somente a chamadas de função, também pode ser utilizada na definição:

```lua
function gato:morre()
    if self.vidas > 0 then
        print("te liga")
        self.vidas = self.vidas - 1
    else
        print("te fudeu")
    end
end

gato:morre()
gato.morre(gato)
```

Perceba que nós utilizamos a palavra-chave `self` para nos referir à própria tabela.

Bom, usando o que aprendemos, vamos criar uma simples classe Player

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
  -- Input, atualiza posição, ...
end

function Player:draw()
  -- Desenha player na tela
end
```

Ah, detalhe importante, note que:

```lua
local p = Player
local p1 = Player

print(p.x, p1.x) -- printa 0 0
p:init(92, 0)
print(p.x, p1.x) -- printa 92 92

print(Player.x) -- printa 92
```

  Ao associar `Player` à `p`, o valor não é copiado, mas sim `p` agora aponta para a table `Player`. Sendo assim, como podemos criar instâncias de uma classe? Poderíamos fazer algo como:

```lua
local Player = {}
function Player.create(x, y)
    local t = {}
    t.x = x or 0
    t.y = y or 0
    t.update = function(sf,dt) end
    t.draw = function(sf) end

    return t
end

local p = Player.create(92, 0)
local p1 = Player.create(48, 0)
print(p.x, p1.x) -- printa 92 48
```

Que seria mais algo como uma `factory`, então toda vez que for criado um novo Player, uma nova tabela é criada. Mas a gente consegue melhorar isso com um conceito bem da hora chamado de `metatabelas`. Vamos ver um pouco sobre:

#### Metatabelas

Metatabelas não passam de simples tabelas, porém com algumas funções especiais. Metatabelas sozinhas não conseguem fazer nada, como eu disse, são tabelas Lua normais, você percebe seu uso ao adicioná-la a outra tabela.

Vamos ver um exemplo simples criando uma classe `Vector`

```lua
-- tabela
local Vector = {
  x = 0,
  y = 0
}

-- metatabela
local vec_mt = {}

setmetatable(Vector, vec_mt)
```

E simples assim, utilizando a função `setmetatable()` conseguimos associar uma metatabela à uma tabela.

Como eu falei antes, metatabelas tem um conjunto de funções especiais que podem ser definidas, vamos tentar algumas.
Pra começar nós temos as funções aritméticas, que são chamadas quando tentamos realizar alguma operação com nossa tabela, por exemplo:

- `__add(self, outro)`: é chamada quando tentamos somar nossa tabela com outro valor: `local soma = self + outro`

e seguindo isso temos também as funções `__sub`, `__mul`, `__div` que os nomes já são bem sugestivos.

Beleza, bora aplicar isso na nossa classe `Vector`:

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

local soma = Vector + Vector
print(sum.x, sum.y) -- vai printar 40 180

-- Perceba que você não consegue chamar
local sum2 = sum + sum

-- Porque 'soma', que é a tabela retornada por '__add', não possui metatabela.
```

Existem várias outras funções, elas são chamadas de `Eventos de Metatabela`, e você pode dar uma olhada aqui [http://lua-users.org/wiki/MetatableEvents](http://lua-users.org/wiki/MetatableEvents)

Existem dois em especial que são bastante úteis, com eles você consegue criar um comportamento bem parecido com Orientação a Objetos, são eles `__index` e `__call`. Lembra do nosso exemplo da classe `Player`? Vamos retomá-lo:

```lua
local Player = {}
Player.x = 0
Player.y = 0

function Player:update(dt)
end

function Player:draw()
end

function Player:attack(dmg)
    dmg = dmg or 0
    print('ataque: ' .. dmg .. ' de dano')
end

local p = {}
setmetatable(p, { __index = Player })

p:attack(15) -- printa 'ataque: 15 de dano'
```

Perceba que `p` é uma tabela vazia, mas nós conseguimos chamar a função `attack`. Isso é graças ao meta evento `__index`, `{ __index = Player }` é exatamente nossa metatabela, o `__index`, ele pode ser tanto uma função quanto uma tabela, toda vez que tentarmos acessar um campo em nossa tabela `tabela["chave"]` e ele não existe, a busca prossegue para o `__index` da nossa metatabela, e caso o valor seja uma tabela, então o que acontece é `__index["chave"]`.
E pra provar isso podemos fazer um teste bem simples utilizando a função `getmetatable`, que nos retorna a metatabela associada a tabela:

```lua
local mt = getmetatable(p)

print(mt.__index == Player) -- 'true'
mt.__index:attack(15) -- 'ataque: 15 de dano'
```

Viu, `mt` é nossa metatabela, e ela é exatamente igual à Player. Agora vamos ver como podemos criar um construtor para nossa classe utilizando `__index`:

```lua
local Player = {}
Player.__index = Player

function Player:new(x, y)
    local o = setmetatable({}, Player)
    o.x = x or 0
    o.y = y or 0
    return o
end

function Player:update(dt)
end

function Player:draw()
end

local p = Player:new(0, 32)
print(p.x, p.y) -- '0 32'
```

Perceba que temos `__index` em `Player`, pois posteriormente usamos a tabela como a própria metatabela `setmetatable({}, Player)`.

Utilizando esses conceitos, podemos criar até coisas mais interessantes como Herança e Polimorfismo. Vamos à um outro exemplo:

```lua
local Animal = {}
Animal.__index = Animal

function Animal:new(pernas)
    local o = setmetatable({}, Animal)
    o.pernas = pernas
    return o
end

function Animal:info()
    print('pernas:', self.pernas)
end

local cachorro = Animal:new(4)
local cobra = Animal:new(0)

cachorro:info() -- 'pernas: 4'
cobra:info() -- 'pernas: 0'
```

E a vantagem de se utilizar `__index` é que qualquer função que eu vier a adicionar à `Animal` posteriormente, poderá ser acessada pelas instâncias.

```lua
function Animal:temPernas()
  return self.pernas > 0
end

print(cachorro:temPernas()) -- 'true'
print(cobra:temPernas()) -- 'false'
```

Tá, mas e se eu quero adicionar funções específicas para cada tipo de animal. Por exemplo, quero adicionar uma ação "latir", não faz sentido cobra ter esse método. Para fazer isso podemos utilizar o conceito de Herança.

Para isso vamos mudar um pouco o nosso construtor.

```lua
function Animal:new(...) -- quando utilizamos isso, significa que vamos passar um número variável de argumentos

  -- irei explicar sobre o '__index = self' depois
  local o = setmetatable({}, { __index = self })
  o:construtor(...) -- Chama uma função chamada construtor com os argumentos que nós passarmos nesta função
  return o 
end

function Animal:construtor(pernas)
  self.pernas = pernas
end

function Animal:info()
    print('pernas:', self.pernas)
end
```

Ao passar múltiplos argumentos na função `Animal:new` eu posso torná-la genérica, e assim funciona com qualquer argumento. Isso é interessante porque podemos fazer diferentes construtores para cada classe. Vamos criar uma classe `Cachorro` que herda de `Animal`:

```lua
local Cachorro = setmetatable({}, Animal)

-- sobrescreve a função do construtor
function Cachorro:construtor(raca)
    Animal.construtor(self, 4) -- chama o construtor do Animal passando 4 (número de pernas) como argumento
    self.raca = raca
end

function Cachorro:latir()
    print('au au!')
end

function Cachorro:info()
    Animal.info(self)
    print('raça:', self.raca)
end

cachorro = Cachorro:new('Pinscher')
cachorro:latir()
```

Nós precisamos fazer
```lua
local o = setmetatable({}, {__index = self})
```
porque precisamos garantir que toda vez que `new` for chamado, a classe certa seja passada. Lembre-se que `self` é a tabela que é passada como primeiro argumento, então é o mesmo que fazer:

```lua
local cobra = Animal.new(Animal, 0)
local cachorro = Animal.new(Cachorro, 'Pinscher')
```

E para finalizar, vamos falar sobre o meta evento `__call`, que felizmente é extremamente simples, é uma função que será chamada que tentarmos chamar uma tabela como função. Por exemplo:

```lua
local tb = { x = 10 }

tb() -- vai dar erro
```

Porém ao adicionarmos uma metatabela com o campo `__call`:

```lua
local tb = { x = 10 }
local mt = {
  __call = function(self, num)
    return num * self.x
  end
}
setmetatable(tb, mt)

print(tb(10))
```

E usando tudo que aprendemos, podemos criar uma simples biblioteca de pseudo Orientação a Objetos:

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
  print("GAME OBJECT")
  self.x = x or 0
  self.y = y or 0
end

function GameObject:move(x, y)
  self.x = self.x + x
  self.y = self.y + y
  print("nova posição:", self.x, self.y)
end

local Player = GameObject:extend()

function Player:constructor(x, y)
  GameObject.constructor(self, x, y)
  print("PLAYER")
end

local Enemy = GameObject:extend()

function Enemy:constructor(x, y)
  GameObject.constructor(self, x, y)
  print("ENEMY")
end

local player = Player(20, 10)
player:move(20, 40)


local enemy = Enemy(5, 45)
enemy:move(10, 90)
```

E é basicamente isso, a ideia era mostrar um pouco como funciona a linguagem Lua. Novamente, se você não sabe nada da linguagem recomendo dar uma estudada antes usando tutoriais e a [documentação](https://www.lua.org/manual/5.2/pt/manual.html).

No próximo tutorial, vamos tentar (finalmente) começãr com LÖVE.
