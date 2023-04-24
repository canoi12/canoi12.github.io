---
layout: post
title: bitEngine - criando janelas multiplataforma
date: '2023-04-23T10:02:00'
type: Blog
tags:
- article
- pt-br
---

Tirando um pouco da poeira daqui.

Dessa vez decidir começar um projeto sobre uma parte que venho querendo aprender a um tempo, que seria como criar o contexto básico pra um jogo (janela, gráficos e áudios) utilizando somente bibliotecas do próprio sistema, em resumo, eu queria entender mais como bibliotecas como [SDL2](https://libsdl.org) e [GLFW](https://libglfw.org) funcionam.

Nisso (como sempre faço na minha vida) decidi criar um projeto pra focar nos estudos dessa parada, [bite](https://github.com/canoi12/bite), a ideia é:

- Conseguir criar uma janela pelo menos em desktop (Windows, Linux e Mac) e web (WebGL com Emscripten)
- Criar um render básico utilizando OpenGL (OpenGLES2 com Emscripten), e carregar somente algumas funções específicas do modern OpenGL que vão ser necessárias (criar shaders, framebuffers, ...).
- Tocar pelo menos 1 áudio (a ideia é fazer um mixer, mas vamo vê né).
- Filesystem básico (ler e escrever arquivos, listar diretórios, ...).

A ideia é ter algo como:
```c
#include <bite.h>

int main(int argc, char** argv) {
	be_Config conf = bite_init_config("Hello Window", 640, 380);
	be_Context* ctx = bite_create(&conf);
	while (!bite_should_close(ctx)) {
		bite_poll_events(ctx);
		// render stuff
		bite_swap(ctx);
	}	
	bite_destroy(ctx);
	return 0;
}
```

Onde por trás vai ser criado o contexto específico pra cada plataforma:
```c
be_Context* bite_create(const be_Config* conf) {
#if defined(_WIN32)
	// Win32 Window and WGL context creation
#elif defined(__EMSCRIPTEN__)
	// Emscripten context creation
#else
	// Linux Window and GLX context creation
	// other systems .....
#endif
}
```

Se estiverem interessados em como funciona a criação da janela pra cada plataforma, esse artigo dá uma pincelada legal no assunto: https://zserge.com/posts/fenster/

Na parte de renderização vai OpenGL mesmo, que como eu disse funciona bem pro meu escopo (Desktop e Web). Pra isso preciso carregar um contexto OpenGL que suporte as funções mais novas/extensões, já que as libs padrão de cada plataforma (GLX no Windows e WGL no Windows) só nos dá um contexto com uma versão antiga (versão 1.4 se não me engano), e cada plataforma tem sua maneira de carregar um contexto mais moderno. No Windows, por exemplo, é necessário criar uma "dummy window" com um contexto antigo somente pra ser capaz de carregar a função responsável por criar o contexto mais novo, depois disso ela é simplesmente deletada, no Linux não é necessário (outras plataformas provavelmente tem suas especificidades também, mas não cheguei lá ainda).

https://gist.github.com/nickrolfe/1127313ed1dbf80254b614a721b3ee9c (Windows)
https://apoorvaj.io/creating-a-modern-opengl-context/ (Linux)

Tendo o "contexto moderno" carregado, ainda é preciso carregar as funções que eu vou utilizar, e pra isso existe a função "GetProcAddress" de cada lib (glXGetProcAddress no Linux ou wglGetProcAddress no Windows).
```c
typedef GLuint glCreateProgramProc(void);

static glCreateProgramProc* glCreateProgram = 0;

#if defined(_WIN32)
	#define biteGetProcAddress wglGetProcAddress
#elif (__linux__)
	#define biteGetProcAdress glXGetProcAddress
#endif

int init_opengl_procs(void) {
	glCreateProgram = (glCreateProgramProc*)biteGetProcAdress("glCreateProgram");
	return 0;
}
```

Vale a pena dar uma olhada em outros loaders como o [glad](https://glad.dav1d.de/) e o [GLEW](https://glew.sourceforge.net/).

Tem uma lib minha que faz algo parecido com o que eu quero fazer aqui, [tea](https://github.com/cafe-engine/tea), que é basicamente carregar somente o mínimo de funções necessárias e criar abstrações em cima delas.

Pra ter o básico pra suportar shaders, por exemplo, seriam necessárias:

- glCreateShader
- glShaderSource
- glCompileShader
- glGetShaderiv
- glGetShaderInfoLog
- glDeleteShader

- glCreateProgram
- glAttachShader
- glLinkProgram
- glGetProgramiv
- glGetProgramInfoLog
- glDeleteProgram

- glUseProgram

Obviamente sem expor isso pro usuário, mas sim abstraindo o processo em outras funções:
```c
be_Shader* bite_create_shader(const char* vert_src, const char* frag_src) {
	be_Shader* shader = NULL;
	GLuint program;
	GLuint vert, frag;

	vert = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vert_src);
	// ....
	
	program = glCreateProgram();
	glAttachShader(program, vert);
	glAttachShader(program, frag);
	// ...

	shader->handle = program;
	glDeleteShader(vert);
	glDeleteShader(frag);
		

	return shader;
}
```

E é isto.

Fora a renderização também vão ter outros pontos pra se lidar, como por exemplo:

- Eventos (janela movendo/redimensionando, tecla pressionada, ...), que na real é bem tranquilo, o mais chatinho é lidar com a questão multiplataforma da parada mesmo, já que no caso de teclas pressionadas os KeyCodes são diferentes, por exemplo, então tu vai ter que criar os seus próprios e filtrar por plataforma pra retornar pro usuário o certo.
- Áudio, que sinceramente ainda é um mistério pra mim.
