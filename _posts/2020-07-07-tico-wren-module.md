---
layout: post
title: (Re)Implementing Wren in tiny coffee
date: '2020-07-07T11:25:00-03:00'
type: Devlog
tags:
- tinycoffee
- tico
- programming
- gamedev
- devlog
---

I'm working in re-implement Wren in tiny coffee, i started with Wren support first, but as i'm more familiar with Lua, and had more material on internet, i decided to focus on it. But as things seems getting fine with the Lua wrap, i decided to work again in the Wren wrap.

In the beginning i was thinking in have two exe options, with Lua and with Wren, but i decided to make Lua mandatory, cause i want to write the game editor and tools in Lua, and organize with modules to users create their own tools and share with each other. With Lua being mandatory, i decided to distribute the exe with Wren too. But how to determine how language to use? I thought in many solutions (detect for `main.lua` or `main.wren`, make Lua default and load Wren from Lua script), but decided to go with the easier one, just let user choose in `config.json` :v

```json
{
	"name": "project",
	"lang": "wren"
}
```

I writed an `init.lua` to init Lua code and still writing an `init.wren`.

Actually tico supports LuaJIT and Lua 5.4, still deciding if i distribute the 2 exe, or just LuaJIT, and if user wants, it compile with Lua 5.4 from the repo.

i'll divide Wren wrap in the same modules as Lua.

- tico.graphics
- tico.audio
- tico.filesystem
- tico.json
- tico.window
- tico.input

And as in Wren everything is a class, each module implement their own classes:

- tico.graphics
	- Graphics
	- Image
	- Canvas
	- Shader
- tico.audio
	- Audio
	- Sound
- tico.filesystem
	- Filesystem
	- File
- tico.json
	- JSON
- tico.window
	- Window
- tico.input
	- Keyboard
	- Mouse
	- Joystick
	- Input

About the limitations, as i still learning about Wren, i need to figure out how to implement certain things. Different from Lua, that almost all your game structures are treated as tables, in Wren they are classes, but in Lua, specifically talking about the C API, you can pass a by argument and deal with it, in Wren i think you can't handle a Wren class if it is not a `foreign class` (equivalent to `userdata` in Lua), and can't handle `objects` too, i'm not 100% sure and still studying. But if this is in fact a true limitation, i'm need to figure out how to implement certain modules in different ways, `JSON` for example, in Lua i can convert Lua table to JSON string and JSON string to Lua table, in wren i still need to figure out other way to do that.

But that's it, Wren is a growing language, lightweight, very fast, and comes only with the necessary, and i need to learn how to deal with the aspects of the language.