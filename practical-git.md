# Git Práctico

## Conceptos clave

Antes de empezar a soltar comandos como un bellaco, hay que explicar los conceptos clave que se necesitan conocer para trabajar con Git.

Comenzaremos con los diferentes estadios en los que puede encontrarse nuestro código (nuestros cambios sobre el contenido de los ficheros, en realidad).

1. **Workspace**: Es el estado real de nuestros ficheros. Tal y como los vemos en nuestro editor.
2. **Stage**: Aquí se encuentran los cambios sobre nuestros ficheros que se incluirán en el próximo _commit_. Cuando hacemos un `git add`, un `git rm` o un `git mv`, estamos metiendo cambios en el _stage_, indicándole a Git que en el próximo _commit_ esos cambios irán incluidos.
3. **Commits** (locales): Cada _commit_ es un grupo de cambios sobre uno o varios ficheros, con una descripción, una fecha, un autor, etc. La gran diferencia con _SVN_ es que los commits en _Git_ son locales hasta que no se hace la subida al servidor. Estos commits locales (**importante lo de que sean locales**) pueden ser modificados sin peligro (con _modificados_ quiero decir que se les pueden añadir más cambios, modificarles el mensaje o incluso eliminarlos).
4. **Commits** (remotos): Cuando se suben cambios al servidor (o como se le llama en Git: el _remoto_), se considera que estos entran a formar parte del histórico compartido entre los desarrolladores del proyecto y, por lo tanto, no es buena práctica modificarlos como se hace cuando los _commits_ son locales (y además hacerlo puede provocar importantes quebraderos de cabeza).

En resumen, el _flow_ de trabajo con Git es:

1. Hago cambios en mis ficheros (**workspace**)
2. Añado al **stage** los cambios que quiero commitear
3. Hago el **commit**
4. Subo los cambios al remoto

## Comandos específicos

### Clonar repositorios

Para bajarse un proyecto de un repositorio existente, necesitamos la URL de ese repositorio. Una vez la tengamos (y nos hayan dado los permisos pertinentes), podemos descargarlo con:

```sh
$ cd ~/dev/
$ git clone https://github.com/twbs/bootstrap.git
$ cd bootstrap/
```

Como veis, por defecto `git clone` nos mete el proyecto en un directorio con el nombre del repositorio. Si preferimos indicarle el nombre del directorio donde lo queremos meter, podemos hacer:

```sh
$ cd ~/dev/
$ mkdir my-awesome-bootstrap/
$ git clone https://github.com/twbs/bootstrap.git my-awesome-bootstrap
$ cd my-awesome-bootstrap/
```

### Ver el estado de nuestro _stage_ y _workspace_

Un comando que se usa **muy** a menudo es `git status`, con el que obtenemos información sobre el estado de nuestro _stage_ y nuestro _workspace_. Es decir, información sobre qué tenemos añadido para formar parte del próximo _commit_, qué no, qué ficheros son nuevos en el sistema y Git aún no conoce, etc.

Cuando ejecutamos `git status`, se nos mostrará algo tal que:

```sh
$ git status
On branch master

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    file-deleted.txt
        modified:   file2-modified.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   file1-modified.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        new-file.txt
```

Cada sección indica en qué estado están los cambios nuestro _workspace_ con respecto al _stage_.

* **Changes to be commited**: Esta sección muestra los cambios añadidos al _stage_, es decir, los que formarán parte del próximo commit. Junto a cada fichero se muestra qué se ha hecho con él: **modified**, **deleted**, **added**…
* **Changes not staged for commit**: En esta sección se muestran los cambios que se han hecho sobre nuestros ficheros, pero que no han sido añadidos al _stage_ y por tanto **no** formarán parte del próximo commit. Al igual que en la sección anterior, se muestra el tipo de cambio que se ha hecho sobre cada fichero: **modified**, **deleted**, **added**…
* **Untracked files**: Aquí se ven los ficheros que están en nuestro _workspace_ de los que Git no tiene conocimiento aún. Es decir, no forman parte del control de versiones y por tanto, lógicamente, no formarán parte del próximo _commit_.

Si leemos atentamente el resultado de `git status`, podemos ver que se nos ofrecen consejos sobre qué hacer con cada fichero en cada sección:

En **Changes to be commited** nos dice:

```sh
use "git reset HEAD <file>..." to unstage
```

Es decir, si queremos hacer _unstage_ (sacar del _stage_) de los cambios sobre `file2-modified.txt`, debemos ejecutar `git reset HEAD file2-modified.txt`.

### Añadir cambios al _stage_

Una vez hemos editado nuestros ficheros, podemos añadir los cambios al _stage_ usando:

```sh
$ git add .
$ git add file1.txt file2.js file3.html
```

Si lo que queremos es mover ficheros, podemos hacerlo directamente desde Git:

```sh
$ git mv file.txt renamed.txt
$ git mv file.txt other/folder/
```

Pero lo normal es que lo hagamos desde nuestro editor, o desde el explorador de ficheros del sistema operativo. Si lo hacemos así, Git no sabrá cuál era el nombre antiguo del fichero y cuál es el nuevo (o cuál era la ruta antigua y cuál es la nueva). Pensará que hay un fichero nuevo en el sistema (el nombre o ruta nueva), y uno que hemos eliminado (el nombre o ruta antigua).

En estos casos, para indicarle a Git el renombrado (o movimiento de ficheros), sólo tenemos que decirle que añada el nuevo nombre y elimine el antiguo:

```sh
$ mv file.txt renamed.txt
$ git add renamed.txt
$ git rm file.txt
```

Otra cosa que podemos querer hacer es eliminar ficheros. Hacerlo desde Git es tan simple como:

```sh
$ git rm file.txt file2.txt
```

Al igual que cuando movemos ficheros, lo normal es hacerlo desde fuera de Git (nuestro editor, el explorador de ficherso…). Si lo hemos hecho así, la forma de añadir esa eliminación al _stage_ es la misma:

```sh
$ rm file.txt
$ git rm file.txt
```
Algo que suele ocurrir, es que queramos añadir al _stage_ modificaciones sobre ficheros y también la eliminación de otros tantos. Para ello, con lo explicado hasta ahora habría que hacerlo en dos pasos:

```sh
$ git add modified-file1.txt modified-file2.txt
$ git rm deleted-file.txt deleted-file2.txt
```

Pero si queremos ahorrarnos estos pasos y añadir todos los cambios sobre los ficheros que Git conoce y no están añadidos al _stage_, podemos hacerlo de golpe con:

```sh
$ git add --all
```

### Sacar cambios del _stage_

Como ya hemos visto antes gracias a la información de `git status`, para sacar cambios que estén añadidos al _stage_ podemos usar `git reset HEAD file.txt`.

Para sacar todos los cambios del _stage_:

```sh
$ git reset HEAD .
```

Para sacar cambios concretos:

```sh
$ git reset HEAD file1.txt file2.js file3.html

```

### Deshacer cambios sobre ficheros del _workspace_

En muchas ocasiones nos encontraremos con que hemos hecho cambios sobre ficheros, o que hemos eliminado ficheros, y queremos volver a la versión previa a esos cambios.

Para deshacer los cambios sobre todos los ficheros:

```sh
$ git checkout -- .
```
Para deshacer los cambios sobre ficheros concretos:

```sh
$ git checkout -- file1.txt file2.js file3.html
```

### Commit

Una vez tenemos los cambios en nuestro _stage_, el siguiente paso es hacer el _commit_.

La manera más simple es:

```sh
$ git commit
```

Al hacerlo así se abrirá el editor del sistema para que escribamos el mensaje asociado al _commit_. Este editor normalmente será `vim` o `nano` en entornos UNIX. Una vez escrito el mensaje, en la primera línea del fichero de texto abierto, sólo tenemos que guardar y cerrar el editor para que el _commit_ se cree.

Si queremos ahorrarnos el paso del editor, podemos especificar el mensaje directamente desde el comando:

```sh
$ git commit -m 'My cool and descriptive message'
```

### Undo

git reset <commit>
git reset HEAD~1

### Revert

git revert <commit>

### Branches

git branch
git branch -va

git checkout -b my-branch
git checkout another-branch

git branch -D my-branch

git push origin :my-branch
