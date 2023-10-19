---
layout: post
title: Selene Engine
date: 2023-10-19T12:37:00
type: Blog
tags:
  - article
  - devlog
  - programming
---

Nas ultimas semanas estava focado em reestruturar um dos meus projetos que é basicamente uma game framework/engine que utilize Lua como linguagem de script, o projeto inicialmente nasceu como [poti](https://github.com/canoi12/poti), mas há algum tempo estava tentando ressignificar outro dos meus projetos, a [selene](https://github.com/canoi12/selene), e por achar que o nome casa mais com o projeto em questão, decidi fazer essa troca.

Inicialmente eu estava focado em fazer a framework base em C e operar ela utilizando Lua, então a ideia seria ter um renderizador básico em C, uma engine de áudio básica também, etc. Maaas, decidi seguir por um caminho diferente, e até explorar mais a ideia inicial do projeto que é a de ter um core simples e o projeto ser o mais modular possível via Lua, então ao invés de construir essas estruturas em C usando as libs (SDL2, OpenGL, ...), achei melhor expor as funções das lib pra Lua e construir a framework lá.

Então por exemplo, uma função de desenhar um retângulo em C:
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

Vira isso:
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

Obviamente isso tem custos em relação a performance, mas dependendo da proposta do projeto funciona muito bem, fora que a ideia é a partir desse ponto construir uma engine em cima disso, então posso usar toda a modularidade que Lua me permite pra otimizar boa parte dessas chamadas de funções, e caso necessário é só refazer partes em C.

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

Onde o mais notório talvez fosse o `graphics` que é um renderer 2D basicão usando OpenGL e `audio` que basicamente é um wrapper pra a miniaudio. E disso eu teria só alguns scripts em Lua pra me auxiliar com boot, construção de shaders, gerenciamento de recursos, etc.

Na selene proposta é um pouco diferente, e de cara já vai ter bem mais código em Lua:

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
- audio
	- Music
	- Sound
- filesystem
- graphics
	- Batch
	- Canvas
	- Font
	- Image
	- Rect
	- Shader
- gamepad
- joystick
- keyboard
- mouse

Bom, feito isso, preciso definir um "entry point" com scripts que digam minha aplicação como iniciar, pra isso decidi ir pela solução mais simples, os módulos em Lua ficam dentro de uma pasta chamada `core/` e localizadas no mesmo diretório do meu executável, e uso um scriptizinho simples embutido no meu .exe que buscar por ela:

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

A ideia posteriormente é dar a possibilidade do dev escrever um `core/` personalizado dentro do próprio projeto, mas no core padrão da selene, como citei anteriormente, a ideia é primeiramente construir a engine:
- Camera
- Sprite
- SceneManager
	- Scene
- AssetManager
	- Asset (Texture, Audio, Sprite, Tileset, Tilemap, ...)
- ...

E usar da modularidade do Lua pra fazer um runner e um editor, Também está nos planos de criar um módulo em C para lidar com `.zip` e um módulo em Lua pra fazer o build system usando isso, pra ser possível distribuir o executável junto do zip (ou concatenar em um executável único, mas isso mais pra frente).

Também está nos planos fazer alguns comandos:

`./selene [build|run|edit|...] path/to/project`

Enfim, até então é bem isso, também pretendo fazer um Makefile depois pra quem tiver interesse em compilar com LuaJIT, que só não uso por padrão por conta da portabilidade, então prefiro seguir usando o Lua padrão.