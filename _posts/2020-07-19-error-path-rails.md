---
layout: post
title:  "Warning instalando RVM en Ubuntu: PATH is not properly set up"
date:   2020-07-04 15:48 +0200
categories: jekyll update
---

(RVM)[https://rvm.io/] es el gestor de versiones de Ruby que se recomienda utilizar cuando instalamos RoR.

Hubo una época en que debí seguir los pasos en orden y tuve instalado RVM sin problemas en mi ordenador, pero después de algún formateo y de haber ignorado un par de _warnings_ me decidí por prestar atención a la configuración de RVM. Cuando ejecutaba `rvm -v` me encontraba con el siguiente error:

```
Warning! PATH is not properly set up, /home/marisa/.rvm/gems/ruby-2.7.0/bin is not at first place.
         Usually this is caused by shell initialization files. Search for PATH=... entries.
         You can also re-add RVM to your profile by running: rvm get stable --auto-dotfiles
         To fix it temporarily in this shell session run: rvm use ruby-2.7.0
         To ignore this error add rvm_silence_path_mismatch_check_flag=1 to your ~/.rvmrc file.
rvm 1.29.10 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]

```

Yo utilizo ZSH en mi terminal y, a veces, estas instalaciones que crean variables de entorno automáticamente escriben en `.bashrc` en vez de en `.zshrc` así que normalmente soluciono ese tipo de problemas viendo qué es lo que se ha escrito en `.bashrc` y copiándolo en `.zshrc`. Pero esta vez no funcionaba. 

También intenté ejecutar `rvm get stable --auto-dotfiles` pero cuando ejecutaba `rvm -v` seguía devolviendo el mismo _warning_.

