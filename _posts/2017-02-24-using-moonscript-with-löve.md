---
layout: post
title: Using MoonScript with LÖVE
date: '2017-02-24T21:40:00-03:00'
type: Tutorial
tags:
- love2d
- programming
- gamedev
- tutorial
tumblr_url: https://canoigomes.tumblr.com/post/157672900266/using-moonscript-with-l%C3%B6ve
---
In this tutorial, i’ll show you how to use the awesome MoonScript with LÖVE, it’s a very basic tutorial. I discovered MoonScript yesterday, 23 Feb (my birthday haha), and i loved the way the language complements the Lua lang. ![](https://66.media.tumblr.com/f5789927c4a8814d39578c8281a9fa75/tumblr_inline_olwmxwYwpj1uuq5lf_540.png)<!-- more -->

For start, you need to install MoonScript in your machine. I don’t will cover this in the tutorial, so if want to install MoonScript, you can access [this site](https://moonscript.org/#installation).  
 Okay, after install MoonScript, we can start our project. First, we need to create a archive called main.moon.

```moonscript
love.load = ->
love.update = (dt) ->
love.draw = ->
```
Easy, right? haha now we can compile MoonScript code to Lua code, you need to open a terminal in the main.moon folder and use, “moonc main.moon” command. If you have more than one .moon files, you can use “moonc \*.moon”, this will compile all your moon codes to Lua code. Now you can see the .lua files in the same folder.   
 So, now, you can easily use “love .” in that folder to start the program.

Oh thats it?! End of tutorial? haha Nah, now, let’s create a simple application using one of the most cool features of MoonScript, the classes. First, i’ll create a class GameObject.


```moonscript
class GameObject
  x: 0
  y: 0
  width: 32
  height: 32
  update: (dt) =>
  draw: () =>
        
love.load = ->
love.update = (dt) ->
love.draw = ->
```

We created a simple GameObject class, with x, y, width, and height attributes. Remember, if you class have some table as attribute, you will need to create a method called “new”, and initialize your table in it, if you don’t do that, you’ll use the same table for all the objects. For example:

```moonscript
class Teste
  attrtable: {}
  add_value: (value) =>
    table.insert @attrtable, value
    
obj_teste = Teste! -- Create a new "Teste object"
obj_teste2 = Teste!
  
print obj_teste.attrtable[1] -- will print 'nil'
print obj_teste2.attrtable[1] -- will print 'nil'

obj_teste\add_value 1 -- add the value '1' to the table obj obj_teste

print obj_teste.attrtable[1] -- will print 1
print obj_teste2.attrtable[1] -- will print 1 too

obj_teste3 = Teste!
print obj_teste3.attrtable[1] -- will print 1
```

As you can see, if you change the table in one object, will change the table in others too. If you want to solve this “problem”, you can do:

```moonscript
class Teste
  attrtable: {}
  new: =>
    @attrtable = {}
  add_value: (value) =>
    table.insert @attrtable, value
    
obj_teste = Teste!
obj_teste2 = Teste!
  
print obj_teste.attrtable[1] -- will print 'nil'
print obj_teste2.attrtable[1] -- will print 'nil'

obj_teste\add_value 1 -- add the value '1' to the table obj obj_teste

print obj_teste.attrtable[1] -- will print 1
print obj_teste2.attrtable[1] -- will print 'nil'
```

Okay, now we can back to our project haha. Next, create a Player class that inherits of GameObject.

```moonscript
class GameObject
    x: 0
    y: 0
    width: 32
    height: 32
    update: (dt) =>
    draw: () =>
    
class Player extends GameObject
  update: (dt) =>
    if love.keyboard.isDown "left"
      @x -= 200 * dt
    if love.keyboard.isDown "right"
      @x += 200 * dt
  draw: () =>
    love.graphics.rectangle "line", @x, @y, @width, @height

love.load = ->
love.update = (dt) ->
love.draw = ->
```

The Player class reimplement the update and draw methods from GameObject, it’s basically move to the sides when “left or "right” buttons are pressed. Now, lets create a player object, and put it on the screen.

```moonscript
class GameObject
    x: 0
    y: 0
    width: 32
    height: 32
    update: (dt) =>
    draw: () =>

class Player extends GameObject
  update: (dt) =>
    if love.keyboard.isDown "left"
      @x -= 200 * dt
    if love.keyboard.isDown "right"
      @x += 200 * dt
  draw: () =>
    love.graphics.rectangle "line", @x, @y, @width, @height

player = Player!

love.load = ->
love.update = (dt) ->
  player\update dt
love.draw = ->
  player\draw!
```

Thats it! If it’s all ok, you now have a white rectangle in your screen.

Okay, but i need to call `moonc` every change i make in my game? And the codes seems a little mess.

There are two options to solve that, `-w` and `-t`. With `-t` you can choose a directory where you transpiled code will be placed, and with `-w`, it init moonc in watch mode, so it seek for changes in your code and automatically transpile the changed file.

The command will be like this `moonc -t game/ -w .`, basically we're saying "get the code in '.'(current dir) and transpile to 'game/'"

*Remember!!*, your assets and other games stuff need to be put in the `game/` (or whatever you call it) folder, a structure i like to use is:

```
game/
  assets/
  ~lua code~
src/
  ~moon code~
```

`moonc -t game/ -w src/`

For distribution is the same as LÖVE, just get the content in `game/`, zip and distribute.

And this is it, hope you liked!