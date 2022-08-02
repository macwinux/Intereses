---
description: >-
  Es un registro de todo lo hecho en el repositorio, un historial del
  repositorio
---

# Git reflog

Si hemos hecho un reset hard pero nos damos cuenta que queremos regresar a un commit borrado no podemos acceder a su hash con git log, necesitamos:

```
git reflog
```

![Git reflog](<../.gitbook/assets/image (1).png>)

Con esto obtenemos los hash anteriores y podemos volver a un commit borrado con:

```
git reset --hard "commit-hash"
```
