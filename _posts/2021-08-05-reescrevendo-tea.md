---
layout: post
title: Reescrevendo a Tea
date: '2021-08-05T10:35:00'
type: Blog
tags:
- article
- pt-br
---

Bom, e mais uma vez, to reescrevendo uma biblioteca. Primeiramente queria explicar um pouco o motivo por trás, e isso vem da filosofia que pretendo seguir com o projeto [Cafe](https://github.com/cafe-engine), onde eu quero que ela seja construída utilizando libs simples e com finalidades bem específicas:

- Tea: parte gráfica
- Latte: sistema de arquivos
- Mocha: áudio
- Coffee: linguagem de script
- ...

Bom, como citado aí, o objetivo inicial da `Tea` era lidar com tudo que fosse relacionado a gráficos na engine. No projeto que eu estava focando anteriormente, o [tiny coffee](https://github.com/canoi12/tinycoffee), utilizava GLFW para lidar com input e gerenciamento de janelas e OpenGL 3.2 para a renderização, a grande vantagem de se utilizar GLFW era a facilidade de se compilar estaticamente no projeto, porém tem a questão de o render precisar ser todo escrito do zero e com uma API gráfica (OpenGL, Vulkan, ..). Decidir então tentar novamente a [SDL2](https://www.libsdl.org), que já tem um render próprio (além de também dar a possibilidade de criar um context OpenGL e Vulkan) e é extremamente portável.

Bom, resolvido então a questão da biblioteca, sobrou a questão da estrutura do render. SDL apesar de tem um render padrão bem interessante, e o jogo feito somente em cima do mesmo tem uma garantia bem maior de funcionar nas diversas plataformas que a lib suporta, mas como nem tudo são flores, o render é limitado.. Você consegue desenhar texturas, linhas, pontos, retângulos, mas caso queira desenhar circulos, triângulos ou outros polígonos, fica por sua conta implementar utilizando as outras funções que ela já te deu, também tem a questão de não conseguir fazer certas otimizações como um `Sprite Batch` da vida, etc.

Com isso em mente decidi criar 3 backends:

- Baseado em software: Todas as operações de desenho são feitas em cima de um array em C, e depois desenhadas em uma textura SDL normal.
- Baseado no render padrão do SDL2: Basicamente aproveita todas as funções já implementadas por padrão (retângulos, pontos, linhas, texturas, ...), e adiciona algumas novas (circulos, triângulos, ...)
- Baseado em OpenGL: Toda a renderização é feita utilizando OpenGL 3.2, esse modo seria otimizado pra tentar juntar todas os comandos que utilizam a mesma textura pra chamá-los uma única vez (Batched Render)

E depois daí foi o ponto que eu me perdi um pouco com o projeto, como estava utilizando SDL2, o mesmo já vem por padrão com sistemas de input e gerenciamento de janelas, então decidi implementar um wrap para as mesmas na própria Tea. Porém isso me afastava do objetivo inicial, que era o de todas as libs do projeto terem finalidades bem específicas, forem tem bem mais com o que me preocupar no código. Com isso decidir voltar a ideia de focar somente no render, e passar as atribuições de lidar com janela e input (teclado, mouse, joystick, ...) para outra lib ou para a própria `Cafe`, que no momento é só um wrap das libs do projeto para a linguagem Lua, e agora eu pretendo tornar ela mais amigável para se desenvolver jogos também em C.

A primeira mudança é que descarted a ideia de fazer um backend intermediário entre software e OpenGL, focando somente nesses dois. Porém isso vem também com uma mudança na estruturação do projeto, antes eu estava focando em fazer uma biblioteca que fosse mais direta, desenhasse polígonos simples e texturas:

```c
tea_line(float x0, float y0, float x1, float y1);
tea_rect(float x, float y, float w, float h);

tea_color(TEA_WHITE);
tea_line(0, 0, 32, 32);
tea_mode(TEA_FILL);
tea_rect(32, 32, 16, 16);
tea_circle(0, 0, 16);
```

Porém agora pretendo fazer algo mais parecido com OpenGL, onde você só aponta o modo de renderização atual e define as posições dos vertex (ainda estou a definir como vai ser), até então agora parecido com isso:

```c
tea_begin(TEA_LINES);
tea_color(TEA_WHITE);
tea_position(0, 0);
tea_position(32, 32);
tea_end();

tea_begin(TEA_QUADS);
tea_color(TEA_WHITE);
tea_position(32, 32);
tea_position(32+16, 32);
tea_position(32+16, 32+16);
tea_position(32, 32+16);
tea_end();

tea_begin(TEA_TRIANGLES);
// te vira
tea_end();
```

E aí sim utilizando para fazer funções mais diretas na `Cafe`:

```c
cafe_mode(CAFE_FILL);
cafe_color(255, 255, 255, 255);
cafe_triangle(32, 0, 0, 32, 64, 32);
```

Para a parte de OpenGL, quero implementar algo parecido com o que vi no projeto [raylib](https://github.com/raysan5/raylib), o [rlgl](https://github.com/raysan5/raylib/blob/master/src/rlgl.h), onde utilizo macros e tento adaptar diferentes versões do OpenGL na minha estrutura de código, esse render será o principal e o que eu pretendo utilizar na `Cafe`. Então sobrando o render baseado em software, que vai ser nada mais que um projeto secundário, onde quero estudar mais computação gráfica e escrever um rasterizador seguindo esse pipeline do OpenGL (quem sabe até sendo viável fazer jogos 2D e 3D simples, custa nada sonhar).

E é isto, pelo menos alguma atualização do andamento do projeto pra quem tiver interesse.
