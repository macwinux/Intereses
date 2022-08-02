# Git reset

## Sacar archivo del stage

Para sacar un archivo (Readme.md) del stage podemos hacer:

```
git reset HEAD Readme.md
```

Ahora si queremos revertir los cambios del archivo y volverlo al estado del último commit podemos:

```
git checkout -- Readme.md
```

## Deshacer commit

Si se nos ha olvidado commitear algo en el último commit hecho o sencillamente queremos deshacer el commit podemos:

```
git reset --soft HEAD^
```

El archivo quedará de la siguiente manera:

![añadido  y no al mismo tiempo](../.gitbook/assets/image.png)

## Ir a un punto de la historia

Si queremos ir a un punto de la historia pero queremos mantener los cambios de los commit posterires:

```
git reset "commit-number" --mixed
```

## Ir a un punto eliminando los cambios posteriores

Para ello debemos hacer un reset --hard:

```
git reset --hard "commit-number"
```
