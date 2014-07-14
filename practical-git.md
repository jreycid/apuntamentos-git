# Git Práctico desde terminal

## Conceptos clave

Antes de empezar a soltar comandos como un bellaco, hay que explicar los conceptos clave que se necesitan conocer para trabajar con Git.

Comenzaremos con los diferentes estadios en los que puede encontrarse nuestro código (nuestros cambios sobre el contenido de los ficheros, en realidad).

1. **Workspace**: Es el estado real de nuestros ficheros. Tal y como los vemos en nuestro editor.
2. **Stage**: Aquí se encuentran los cambios sobre nuestros ficheros que se incluirán en el próximo _commit_. Cuando hacemos un `git add`, un `git rm` o un `git mv`, estamos introduciendo cambios en el _stage_, indicándole a Git que en el próximo _commit_ esos cambios irán incluidos.
3. **Commits** (locales): Cada _commit_ es un grupo de cambios sobre uno o varios ficheros, con una descripción, una fecha, un autor, etc. La gran diferencia con _SVN_ es que los commits en _Git_ son locales hasta que no se efectúa la subida al servidor. Estos commits locales (**importante que sean locales**) pueden ser modificados sin peligro (con _modificados_ quiero decir que se les pueden añadir más cambios, actualizar su mensaje o incluso eliminarlos).
4. **Commits** (remotos): Cuando se suben cambios al servidor (o como se le llama en Git: el _remoto_), se considera que estos entran a formar parte del histórico compartido entre los desarrolladores del proyecto y, por lo tanto, no es buena práctica modificarlos del mismo modo en que se hace cuando los _commits_ son locales (además hacerlo puede provocar importantes quebraderos de cabeza).

En resumen, el _flow_ de trabajo con Git es:

1. Hago cambios en mis ficheros (**workspace**)
2. Añado al **stage** los cambios que quiero commitear
3. Hago el **commit**
4. Subo los cambios al remoto

## Trabajando con Git

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

### Añadir un servidor a un repositorio local existente

A veces creamos un repositorio local (con `git init`) y no lo tenemos asociado a un repositorio remoto, por lo que no podremos hacer `git push` para subir cambios ya que no hay sitio donde hacerlo.

Si nos dan más adelante una URL para subir nuestro repositorio local a un repositorio remoto, podemos añadirlo como _remote_ usando:

```sh
$ git remote add origin https://github.com/twbs/bootstrap.git
```

Con esto creamos un _remote_ llamado **origin**, asociado a la URL que le hemos pasado.

Para ver los _remotes_ que tiene nuestro repositorio, podemos:

```sh
$ git remote -v
origin https://github.com/twbs/bootstrap.git (fetch)
origin https://github.com/twbs/bootstrap.git (push)
```

### Ver el estado de nuestro _stage_ y _workspace_

Un comando que se usa **muy** a menudo es `git status`, con el que obtenemos información sobre el estado de nuestro _stage_ y nuestro _workspace_. Es decir, información sobre qué tenemos añadido para formar parte del próximo _commit_, qué no, qué ficheros son nuevos en el sistema y Git aún no conoce, etc.

Cuando ejecutamos `git status`, se nos mostrará algo como:

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

Cada sección indica en qué estado están los cambios de nuestro _workspace_ con respecto al _stage_.

* **Changes to be commited**: Esta sección muestra los cambios añadidos al _stage_, es decir, los que formarán parte del próximo commit. Junto a cada fichero se muestra qué se ha hecho con él: **modified**, **deleted**, **added**…
* **Changes not staged for commit**: En esta sección se muestran los cambios que se han hecho sobre nuestros ficheros, pero que no han sido añadidos al _stage_ y por tanto **no** formarán parte del próximo commit. Al igual que en la sección anterior, se muestra el tipo de cambio que se ha hecho sobre cada fichero: **modified**, **deleted**, **added**…
* **Untracked files**: Aquí se ven los ficheros que están en nuestro _workspace_ de los que Git no tiene conocimiento aún. Es decir, no forman parte del control de versiones y por tanto, lógicamente, no formarán parte del próximo _commit_.

Si leemos atentamente el resultado de `git status`, podemos ver que se nos ofrecen consejos sobre qué hacer con cada fichero en cada sección:

En **Changes to be commited** nos dice:

```sh
use "git reset HEAD <file>..." to unstage
```

Es decir, si queremos hacer _unstage_ (sacar del _stage_) de los cambios sobre `file2-modified.txt`, debemos ejecutar `git reset HEAD file2-modified.txt`.

Una vez que estamos familiarizados con Git, el _output_ de `git status` es demasiado grande, con más información de la que realmente se necesita. Podemos mostrar algo más reducido con:

```sh
$ git status -sb
M  file1.txt
D  file2.txt
?? file3.txt
```

De esta manera, podemos ver de manera directa y clara en qué estado está cada fichero (`M` para modificados, `D` para eliminados, `??` para ficheros no _trackeados_…). Y el color de esas letras nos indica si está en el _stage_ (verde) o no (rojo).

### Trabajar con ramas

Una de las características más potentes de Git son las ramas. Gracias a ellas podemos encapsular nuestro trabajo y mantenerlo separado de la línea de desarrollo principal, pudiendo trabajar sin problemas de actualización de código hasta la hora en que hayamos terminado. Esto, además, nos permite cambiar rápidamente de tarea sin que el código _a medio terminar_ nos afecte.

#### Listar ramas

Para listar las ramas existentes en nuestro repositorio, usaremos:

```sh
$ git branch
* master
  dev
```

Esto nos mostrará únicamente las ramas locales, marcando con un asterisco la rama en la que nos encontremos en ese momento. Si queremos ver además las ramas remotas, podemos hacerlo con:

```sh
$ git branch -a
* master
  dev
  remotes/origin/master
  remotes/origin/dev
```

Para mostrar algo de información sobre las ramas, como por ejemplo el último commit que hay en cada una de ellas:

```sh
$ git branch -va
* master                  bed4c52 Sample commit
  dev                     bd81885 Another commit
  remotes/origin/master   bed4c52 Sample commit
  remotes/origin/dev      bed4c52 Sample commit
```

#### Crear ramas

Para crear una nueva rama en nuestro repositorio, y además movernos a ella, usaremos:

```sh
$ git checkout -b my-branch
```

Es muy importante saber que la nueva rama que estamos creando estará basada en la rama en la que nos encontremos en ese momento. Es decir, si nos encontramos en **master**, cuyo último _commit_ es el _bed4c52_, la rama **my-branch** recién creada será una copia de **master**, con ese mismo _commit_ como último.

También hay que tener en cuenta que al crear nuevas ramas, los cambios sobre el _workspace_ y sobre el _stage_ que tengamos en ese momento se mantienen.

#### Movernos entre ramas

Para movernos entre ramas tan sólo tenemos que usar:

```sh
$ git checkout another-branch
```

Es importante tener en cuenta que al movernos entre ramas, los cambios sobre el _workspace_ y sobre el _stage_ que tengamos en ese momento se mantienen, salvo que se vean afectados por el estado de la rama destino. Es decir, si la rama a la que vamos afecta (entra en conflicto) a los cambios que tengamos en nuestro _workspace_ o _stage_, no se realizará el cambio de rama.

Para poder hacerlo tendremos que limpiar el _workspace_ y el _stage_ antes de movernos de rama. Para ello podemos seguir dos estrategias diferentes:

* **Hacer un commit de todos los cambios**: No es lo recomendado, ya que estaremos haciendo un _commit_ con algo que, presumiblemente, aún no está terminado.
* **Meter los cambios en el stash**: Guardamos los cambios en el _stash_ para sacarlos posteriormente (aún no hemos hablado del _stash_, pero lo haremos pronto).

Con cualquiera de estas dos estrategias, limpiaremos nuestro _stage_ y _workspace_ y podremos movernos de rama sin problemas.

#### Eliminar ramas

Para eliminar una rama, usaremos:

```sh
$ git branch -D my-branch
```

### Mostrar el histórico de _commits_

Para mostrar un listado con los _commits_ de la rama en la que estemos en ese momento, debemos usar `git log`:

```sh
$ git log
commit bed4c52fedbb1faf9989e2410b5f0726d24c7e9c
Author: Juan G. Hurtado <juan.g.hurtado@gmail.com>
Date:   Thu Jul 10 13:31:28 2014 +0200

    Update FormPreview styles to match Builder

commit 4de89ea88baad0169bc49d69041fb68095e27c21
Author: Juan G. Hurtado <juan.g.hurtado@gmail.com>
Date:   Wed Jul 9 12:28:44 2014 +0200

    Remove unwanted attrs from Items on creation

commit d818852126e890394612ce5509fb9d5f679f7ff0
Author: Juan G. Hurtado <juan.g.hurtado@gmail.com>
Date:   Wed Jul 9 12:05:59 2014 +0200

    Version bump: v0.0.3
```

Como podéis ver, se muestra un listado con todos los _commits_ de la rama en la que estamos, ordenado cronológicamente, con los más actuales arriba.

Por cada _commit_ se muestra su identificador, el autor, la fecha en la que se hizo y el mensaje que se le dió.

La manera por defecto con la que `git log` muestra el histórico de _commits_ no me parece la más cómoda. Personalmente prefiero:

```sh
* bed4c52 - (HEAD, master, dev) Update FormPreview styles to match Builder (2 days ago) <Juan G. Hurtado>
* 4de89ea - Remove unwanted attrs from Items on creation (3 days ago) <Juan G. Hurtado>
* d818852 - (tag: v0.0.3, origin/master) Version bump: v0.0.3 (2 weeks ago) <Juan G. Hurtado>
```

Para conseguirlo sólo hay que escribir el siguiente comando:

```sh
$ git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

Mucho mejor, ¿verdad? Y mucho más cómodo…

Vale, vale, es broma. Para poder usar cómodamente este comando para mostrar el histórico de _commits_ podéis crear un alias de la siguiente forma:

```sh
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

Y a partir de ahí usar `git lg` para ver el histórico.

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

En estos casos, para indicarle a Git el renombrado (o movimiento de ficheros), tenemos que decirle que añada el nuevo nombre y elimine el antiguo:

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
Algo que suele ocurrir, es que queramos añadir al _stage_ modificaciones sobre ficheros, así como la eliminación de otros tantos. Para ello, con lo explicado hasta ahora habría que hacerlo en dos pasos:

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

### Crear _commits_

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

### Modificar el último _commit_

Si nos hemos olvidado de añadir algo al último _commit_ y **aún no lo hemos subido al servidor**, podemos arreglarlo de la siguiente forma:

```sh
$ git add forgotten-file.txt
$ git commit --amend
```

Al hacerlo, se nos abrirá el editor con el mensaje del último _commit_ listo para ser modificado (si queremos). Una vez guardado y cerrado el editor, se modificará el _commit_ con los cambios sobre `forgotten-file.txt` añadidos.

Si queremos modificar el _commit_ y reutilizar su mensaje, ahorrándonos todo el tema del editor, podemos hacerlo con:

```sh
$ git commit --amend -C HEAD
```

### Eliminar _commits_

Algo que también se hace relativamente a menudo es eliminar commits. Es muy importante tener en cuenta que **no se deben eliminar commits que ya se hayan subido al remoto**. Lo normal es querer eliminar el último _commit_ (o los últimos hasta un punto) que aún no se han subido.

Para hacerlo debemos usar `git reset`, dándole el identificador del commit al que queremos volver. Esto es importante, ya que `reset` significa _"llévame hasta este commit, eliminando los que hay por encima"_.

```sh
$ git reset 4de89ea
```
Una manera rápida de eliminar **el último commit** sin tener que buscar el identificador del commit anterior es:

```sh
$ git reset HEAD~1
```

Hay que tener en cuenta que por defecto, después de ejecutar `git reset`, los cambios de los _commits_ eliminados se mantienen en el _workspace_. Es decir: **no se pierden**. A esto se le llama _soft reset_.

Si queremos eliminar los cambios de los _commits_ además de los _commits_ en si mismos, debemos informar a `git reset` de que queremos hacer un _hard reset_:

```sh
$ git reset --hard 4de89ea
```

Y de nuevo, lo repito para que quede muy claro, **no se deben eliminar commits que ya se han subido al servidor**. Si lo hacemos, podemos poner a los compañeros y a nosotros mismos en un buen problema lleno de quebraderos de cabeza.

### Deshacer _commits_

Otra cosa muy común es querer deshacer un _commit_. ¿Qué significa esto? Que queramos revertir los cambios que se introdujeron en un _commit_ concreto.

Con un ejemplo concreto se entiende mejor: Imaginad que hicimos un _commit_ donde metimos un Javascript que añadía un _tooltip_ a todos los enlaces de la página, pero más adelante nos damos cuenta de que no queremos más ese comportamiento.

Podríamos eliminar manualmente esa librería y crear un nuevo _commit_, pero Git es más inteligente que eso y nos deja hacerlo automáticamente:

```sh
$ git revert 4de89ea
```

Al hacer esto se creará un nuevo _commit_ deshaciendo los cambios introducidos en el _commit_ **4de89ea**. Se abrirá el editor con un mensaje de _commit_ predeterminado que podremos modificar y, al guardar y cerrar el editor, el _commit_ deshaciendo los cambios quedará creado.

### Subir cambios al servidor

Una vez que tenemos preparados una serie de _commits_ en local, podemos subirlos al servidor de la siguiente manera:

```sh
$ git push origin master
```

Donde `master` es el nombre de la rama que queremos subir y `origin` el nombre del remoto.

Habrá ocasiones en las que el servidor no nos permita subir debido a que existan arriba cambios que aún no tenemos. Para ello nos deberemos traer los cambios antes de subir (ver siguiente sección).


### Traer cambios del servidor

Para traernos los cambios existentes en el servidor hacia nuestra rama, usaremos:

```sh
$ git pull origin master
```

### Mezclar ramas

Algo muy común cuando se trabaja con Git es mezclar ramas. Imaginemos que estamos trabajando en una _feature_ sobre una rama de desarrollo, y cuando hemos acabado queremos llevar nuestro fantástico código a la rama principal del proyecto. ¿Cómo lo hacemos?

```sh
$ git checkout master
$ git merge my-feature
```

El primer paso es movernos a la rama a la que queremos llevar tus cambios, y el segundo es llamar a `git merge` diciéndole de dónde queremos traer los cambios.

Conceptualmente, `git merge` **se trae los cambios de la rama que se le diga a la rama en la que nos encontramos**.

### Solucionar conflictos

Después de realizar algunas operaciones (como pueden ser `git merge` o `git pull`) es posible que haya conflictos entre nuestro código y el código que nos hemos traído.

Cuando esto ocurra, después de realizar la operación Git nos avisará con un mensaje del tipo:

```sh
$ git pull origin master

From https://github.com/twbs/bootstrap.git
 * branch            master     -> FETCH_HEAD
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Y si hacemos un `git status` veremos algo tal que:

```sh
$ git status
# On branch branch-b
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#
# Unmerged paths:
#   (use "git add ..." to mark resolution)
#
# both modified:      file.txt
#
# no changes added to commit (use "git add" and/or "git commit -a")
```

Aquí vemos como `file.txt` se encuentra dentro de la sección **Unmerged paths**, y está marcada como **both modified**. Eso significa que hay conflictos en ese fichero.

Si abrimos este fichero con alguna herramienta de _diff_ (como `vimdiff`) podremos ver de manera visual cuáles son los conflictos, y resolverlos como nos convenga.

Si abrimos el fichero con un editor de texto, veremos los conflictos de la siguiente manera:

```sh
the number of planets are
<<<<<<< HEAD
nine
=======
eight
>>>>>>> branch-a
```

Esas marcas indican cuáles son nuestros cambios (los de la sección `HEAD`) y cuáles son los cambios de otra persona (en este caso, los marcados en `branch-a`). Para elegir uno de ellos, sólo tenemos que borrar las líneas que no nos interesen y dejar el fichero tal y como lo queramos (borrando las marcas de conflicto y todo lo que no queramos: el objetivo es dejar el fichero tal y como queramos que se quede).

Una vez solucionados los conflictos, podemos proceder a decirle a Git qué ficheros quedan marcados como resueltos. Para ello usaremos:

```sh
$ git add file.txt
```

Cuando ya están todos los conflictos solucionados y marcados como tal, ya se puede crear el _commit_ con la resolución:

```sh
$ git commit
```

Como se puede ver, no le hemos especificado un mensaje al _commit_, por lo que se abrirá el editor del sistema para que escribamos un mensaje. En este caso, al tratarse de un _commit_ de resolución de conflictos, el mensaje ya viene pre-cargado (aunque podemos modificarlo).

### Trabajar con el _stash_

Como hemos comentado antes, hay ocasiones en las que necesitamos guardar los cambios del _workspace_ y del _stage_ para realizar alguna operación (actualizar cambios desde el servidor, cambiar de rama, etc.). Para ello usaremos el _stash_.

Podemos pensar en el _stash_ como una lista que contiene grupos de cambios temporales, que podemos consultar, aplicar en el _workspace_ actual, etc.

Para añadir los cambios actuales al _stash_, usaremos:

```sh
$ git stash
```

Para aplicar en nuestro _workspace_ el último _stash_ guardado:

```sh
$ git stash pop
```

Hay más comandos para trabajar con el _stash_, pero lo más habitual es tener únicamente un elemento en la lista y aplicarlo inmediatamente después del problema que hayamos intentado solventar (cambiar de rama, actualizar con los últimos cambios del remoto, etc.).