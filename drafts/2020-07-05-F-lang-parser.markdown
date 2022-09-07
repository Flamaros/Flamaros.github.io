---
layout: post
title:  "F-lang: Parser"
date:   2020-05-08 15:00:00 +0100
categories: f-lang
comments: true
---

Expliquer pourquoi j'ai changé de symboles pour les pointeurs, je voulais éviter les conflits avec les opérateurs aritméthique afin de simplifier que lors de la génération de l'AST ce soit directement le bon résultat.
Pas besoin d'attendre l'inference de type pour savoir s'il faut déreférencer la variable indiqué par un identifier ou faire une multiplication, cas que l'on a si on utilise * pour les deux opérateurs.