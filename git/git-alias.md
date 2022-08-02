---
description: Podemos usar este comando para crear shortcuts de los comandos
---

# Git alias

Por ejemplo, para el comando log que muestra el arbol de historicos podemos usar el alias lg:

```
git config --global alias.lg "log --oneline --decorate --all --graph"
```

Ahora, simplemente ejecutando git lg, en realidad estamos ejecutando el comando log --oneline ...

```
git lg
```
