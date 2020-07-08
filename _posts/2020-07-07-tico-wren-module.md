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

I'm working on re-implement Wren in tiny coffee, i started with Wren support first, but as i'm more familiar with Lua, and had more material on internet, i decided to focus on it. But as things seems getting fine with the Lua wrap, i decided to work again in the Wren wrap.

In the beginning i was thinking in have two exe options, with Lua or Wren, but since i want to make the game editor and tools with Lua, i decided to make it mandatory. And with Lua being mandatory, i decided to distribute the exe with Wren too. But how to determine how language to use? I thought in many solutions (detect for `main.lua` or `main.wren`, make Lua default and load Wren from Lua script), but decided to go with the easier one, just let user choose it :v. By default it always load Lua first, and you need to specify Wren in the `config.json`:

```json
{
	"name": "project",
	"lang": "wren"
}
```

I writed an `init.lua` to init Lua code and still writing an `init.wren`.

For now, tico supports LuaJIT and Lua 5.4, still deciding if i distribute the 2 exe, or just LuaJIT, and if user wants, he compiles with Lua 5.4 from the repo.

I'll divide Wren wrap in the same modules as Lua.

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
	- Color
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
	
A limitation that Wren has is in yours `Map` classes/objects, we can't deal with it in the C API, and this has impacts in my JSON class. In Lua, i can encode Lua tables in JSON string and decode JSON string in Lua tables, in Wren i still need to figure out how to do it, as i can't easily create/read Maps from C side. Reading the roadmap, they're planning working on that from 0.4.0 (the moment i'm writing it, the last stable version is the 0.3.0). And it will be a game changer.

But that's it, Wren is a growing language, lightweight, very fast, and comes only with the necessary, and i just need to learn how to deal with some aspects of the language.
