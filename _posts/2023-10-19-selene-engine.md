---
layout: post
title: Selene Engine
date: 2023-10-19T21:07:00
type: Blog
tags:
  - article
  - devlog
  - programming
---
In the last weeks i was focusing on restructure one of my projects, this project is basically a game framework/engine that uses Lua as script language, it initially was called [poti](https://github.com/canoi12/poti), but i had another project called [selene](https://github.com/canoi12/selene) that was one of the main first tries on computer graphics stuff (using SDL2 and OpenGL), and i was trying to find another use case, so i decided to switch both projects because `selene` fits well as a Lua project (lua mean moon in portuguese, and selene is the greek goddess of the moon), and `poti` will become my C lib, but don't started to work on it yet.

Initially i was focusing on make the base framework with C and use Lua as scripting language (like LÖVE), se the idea was to have some basics systems in C (like a render, audio system, ...). But i decided to go with another approach and explore the initial idea of "have a simple C core and implement engine stuff in Lua" in a different way, but instead of code in C using the thirdparty libs (SDL2, OpenGL, ...), i will expose the functions of those libs to Lua, and build the framework and engine using only Lua.

So as an example, i have this function to draw a filled rectangle in the screen using C:
```c
static int l_graphics_fill_rectangle(lua_State* L) {
	float *c = RENDER()->color;
	float x, y, w, h;
	x = luaL_checknumber(L, 1);
	y = luaL_checknumber(L, 2);
	w = luaL_checknumber(L, 3);
	h = luaL_checknumber(L, 4);
	set_texture(RENDER()->white_texture);
	float vertices[] = {
		x, y, c[0], c[1], c[2], c[3], 0.f, 0.f,
		x + w, y, c[0], c[1], c[2], c[3], 1.f, 0.f,
		x + w, y + h, c[0], c[1], c[2], c[3], 1.f, 1.f,

		x, y, c[0], c[1], c[2], c[3], 0.f, 0.f,
		x, y + h, c[0], c[1], c[2], c[3], 0.f, 1.f,
		x + w, y + h, c[0], c[1], c[2], c[3], 1.f, 1.f,

    };

	VertexFormat* v = (VertexFormat*)vertices;
	push_vertices(VERTEX(), 6, v);
}
```

Will become that in Lua:
```lua
function graphics.fill_rectangle(x, y, width, height)
	set_image()
	set_draw_mode('triangles')

	local r,g,b,a = table.unpack(current.draw_color)
	local vertex_data = default.batch.data
	default.batch:push(x, y, r, g, b, a, 0.0, 0.0)
	default.batch:push(x+w, y, r, g, b, a, 0.0, 0.0)
	default.batch:push(x+width, y+height, r, g, b, a, 0.0, 0.0)

	default.batch:push(x, y, r, g, b, a, 0.0, 0.0)
	default.batch:push(x+width, y+height, r, g, b, a, 0.0, 0.0)
	default.batch:push(x, y+height, r, g, b, a, 0.0, 0.0)
end
```

Obviously it will have some cost on performance, but depending on the project it will work fine, and the advantage of Lua modularity is that i can optimize to avoid unecessary draw calls or state changes.
Obviamente isso tem custos em relação a performance, mas dependendo da proposta do projeto funciona muito bem, fora que a ideia é a partir desse ponto construir uma engine em cima disso, então posso usar toda a modularidade que Lua me permite pra otimizar boa parte dessas chamadas de funções, e caso necessário é só refazer partes em C.

About the modules, in poti i had that initially:
Bom, falando um pouco dos módulos, inicialmente eu tinha mais ou menos isso:

`poti`
- audio
	- AudioData
- event
- filesystem
	- File
- gamepad
	- Gamepad
- graphics
	- Texture
- joystick
- keyboard
- mouse
- window

`poti (Lua)`
- boot
- shader_factory
- audio_bank

Where almost everything is just a regular wrapper for SDL2 functions and structures, i think the only differents are `graphics` and `audio` that uses OpenGL and miniaudio respectively. And with that modules i'm gonna have some auxiliar embedded Lua scripts too for boot, shader factory, resource manager and others.

Well, Selene is a bit different:
`selene (C)`
- Data
- audio
	- Decoder
- font
- fs
	- File
- gl
	- Buffer
	- Framebuffer
	- Program
	- Shader
	- Texture
	- VertexArray
- image
- linmath
	- Mat4
- sdl2
	- AudioDeviceID
	- AudioStream
	- Event
	- Gamepad
	- GLContext
	- Joystick
	- Window

`selene (Lua)`
- audio (AudioDeviceID)
	- Music (AudioStream, Decoder)
	- Sound (AudioStream, Data)
- filesystem
- graphics (GLContext, Window)
	- Batch (Buffer, Data)
	- Canvas (Texture, Framebuffer)
	- Font (Texture)
	- Image (Texture, Data)
	- Rect
	- Shader (Shader, Program)
- gamepad
- joystick
- keyboard
- mouse

You will notice that this is what i talked before, the C modules are just a wrap to the libraries and the Lua modules became in fact the game framework. And to boot up my application, i use this little Lua script that searches for a `core` core module in my application folder (this script is embedded in the binary):
```lua
local sdl = selene.sdl2
local function add_path(path)
    package.path = path .. '?.lua;' .. path .. '?/init.lua;' .. package.path
end
return function(args)
	add_path(sdl.GetBasepath())
	local core = require 'core'
	core.init(selene.args)
	return core
end
```

Later i want to let the devs write their own `core` module in the project folder. Buuut, for the default Selene core i want to first make the framework, and the build the engine on the top of it:
- Camera
- Sprite
- SceneManager
	- Scene
- AssetManager
	- Asset (Texture, Audio, Sprite, Tileset, Tilemap, ...)
- ...

And use the Lua modularity to make different contexts for a runner and an editor, for example. In the future plans i want to support more modules too, `.zip` for application distribution. Want to make a command line too:

`./selene [build|run|edit|...] path/to/project`

Well, that's it for now, the project is still in progress but i think it has potential for what i want (multiplatform and modularity of all the structure). I want to make some tests with LuaJIT too, but it's not in the plans to support it, mostly because of multiplaform stuff, but i think it can be interesting for those who want more performance on Desktop for example.