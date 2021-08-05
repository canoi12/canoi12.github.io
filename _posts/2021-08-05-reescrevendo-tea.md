---
layout: post
title: Reescrevendo a Tea
date: '2021-08-05T10:35:00'
type: Blog
tags:
- article
- pt-br
---

Bom, e (mais uma vez) to reescrevendo uma biblioteca. Primeiramente queria explicar um pouco o motivo por trás, e isso vem da filosofia que pretendo seguir com o projeto [Cafe](https://github.com/cafe-engine), que é a de que ela seja construida utilizando libs simples e com finalidades bem específicas:

- Tea: parte gráfica
- Latte: sistema de arquivos
- Mocha: áudio
- Coffee: linguagem de script
- ...

Por utilizar a [SDL2](https://www.libsdl.org) na `Tea`, decidi então também escrever um wrap dos seus sistemas de input e gerenciamento de janelas. Porém percebi que isso aos poucos me afastava do meu objetivo inicial de fazer as bibliotecas terem um objetivo direto, fora ter bem mais com o que me preocupar no código. Com isso decidi voltar o projeto a ideia de ser somente um render, e passar as atribuições de lidar com janela e input (teclado, mouse, joystick, ...) para outra lib ou a própria `Cafe` (que no momento não passa de wrap para a linguagem Lua), que eu pretendo tornar mais amigável pra programar jogos também em C.

Beleza, pensado nisso, surge a questão do render em si, minha ideia era ter 3 backends principais: 

- Baseado em software: Todas as operações de desenho são feitas em cima de uma matriz em C
- Baseado no render padrão do SDL2: Basicamente aproveita todas as funções já implementadas por padrão (retângulos, pontos, linhas, texturas, ...)
- Baseado em OpenGL: Toda a renderização é feita utilizando OpenGL 3.2, esse modo seria otimizado pra tentar juntar todas os comandos que utilizam a mesma textura pra chamá-los uma única vez (Batched Render)

A primeira mudança é que agora só tem "dois", baseado em software e em OpenGL, porém isso vem também com uma mudança na estruturação do projeto. Antes eu estava focando em fazer uma biblioteca que fosse mais direta, desenhasse polígonos simples e texturas (e nesse ponto até bem parecida com a SDL2):

```c
tea_line(float x0, float y0, float x1, float y1);
tea_rect(float x, float y, float w, float h);

tea_color(TEA_WHITE);
tea_line(0, 0, 32, 32);
tea_mode(TEA_FILL);
tea_rect(32, 32, 16, 16);
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
```
Para a parte de OpenGL, quero implementar algo parecido com o que vi no projeto [raylib](https://github.com/raysan5/raylib), o [rlgl](https://github.com/raysan5/raylib/blob/master/src/rlgl.h), onde utilizo macros e tento adaptar diferentes versões do OpenGL na minha estrutura de código, esse render será o principal e o que eu pretendo utilizar na Cafe. Então sobrando o render baseado em software, que vai ser mais um projeto secundário, onde quero estudar mais computação gráfica e escrever um rasterizador seguindo esse pipeline do OpenGL (quem sabe até sendo viável fazer jogos 2D e 3D simples, custa nada sonhar).

E é isto, pelo menos alguma atualização do andamento do projeto pra quem tiver interesse.
