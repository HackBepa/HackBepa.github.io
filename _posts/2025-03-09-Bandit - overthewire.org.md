---
layout: post
title: Bandit - overthewire.org
summary: Esta es mi resolucion al wargame bandit de overthewire.org
tags: bandit 
---

overthewire.org es un recurso donde mediante conexiones a una maquina accederemos a diferentes usuarios que serán los "niveles", para pasar al siguiente nivel deberemos resolver el enunciado publicado en la web. 

Esta pagina tiene diferentes dificultades siendo Bandit la mas baja

Para conectarnos a la maquina mediante SSH, usaremos el siguiente comando:

`$ ssh bandit0@bandit.labs.overthewire.org -p 2220`

Un consejo cuando entremos a los usuarios banditX, normalmente no nos dejara ejecutar comandos como clear o hacer ctrl + L,
para poder navegar de manera normal pondremos `export TERM=xterm`.

