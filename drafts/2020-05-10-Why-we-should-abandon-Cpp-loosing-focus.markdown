---
layout: post
title:  "Why we should abandon C++: loosing focus"
date:   2020-05-10 15:00:00 +0100
categories: C++
comments: true
---




. et -> for pointers
reference
forward declaration
pch
Build incrémental qui foire
CMake /IDE qui foirent (ne voient pas les modifications de leur fichiers de configuration, manipulation à la main)
   - Perte de temps supplémentaire avec la compilation
compiler specific options
compiler specific warnings and default warnings (portability issues)
ABI issues
  - Impact coding style by a lot if you are doing librairies in C++
Visibilité:
  - you have to think what to expose to your users
    - Instead some experienced developer just let everything public to reduce friction,...

Correct inclusion for template meta-programming (sfinae) or for operator overloading,... because in some case wrong overload function can be took.

header guards



Faire attention aux modifications qu'on fait afin de minimiser le prochain build (pas de formattage de code dans certains headers, sauf si c'est le but du dev) ou savoir s'il faut relancer CMake ou faire un rebuild complet (car dans certains cas le build incrémental déconne)





Les messages d'erreurs des compilateurs sont si longs, se répètent souvent que souvent j'ai pas envie de les lire, ils devraient être bien plus user friendly dans leur affichage. Il faut parfois faire un effort important pour retrouver ce à quoi ils font référence.
Trouver un exemple de message vraiment mal placé, mauvais type de valeur de retour d'une implémentation par rapport à la déclaration.