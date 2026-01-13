# Práctica 1

## Desarrollo de la práctica
### Uso del comando `git clone`

El comando git clone sirve para crear una copia de un repositorio git (en este caso de un repositiorio que está hosteado en Github). Se crea una carpeta nueva y se descargan todos los archivos almacenados en ese repositorio. Además, se configura git creando una carpeta llamada `.git` que permite informar al cliente de git que el proyecto está conectado al repositorio original de Github. Así, en caso de querer hacer cambios al repositorio, solo tendría que hacer un push para poder publicarlos. O en caso de que cambien los archivos subidos en remoto, yo podré descargarlos ejecutando el comando `git pull`.

![](photos/2026-01-13%2011-09-44.png)

El output del comando dice que ha empezado a clonar la carpeta `p1`, después enumera el número de objetos a descargar, en este caso serán 6, debido a que cada objeto representa una parte fundamental del funcionamiento de git. En este caso se puede dividir en:
-	blobs (el contenido del los archivos)
-	trees (la estructura del directorio)
-	commit (el cambio atómico hecho en el repositorio)
-	…

En este caso mediante el comando `git rev-list --objects –all` podemos ver los objetos que tiene el repositorio:

```sh
@Tatuck ➜ /workspaces/p1-fork (main) $ git rev-list --objects --all
07720b55268c921986ba40aaf84ff160295d0bc5
a77a53f73ccd621789224c2084d55bcb0fd91219
9006e4f367ab86086cfd51435f3e7320a13fdd69 
8b9c7b04b5d6a40394fc3f47cfff455f7a8edf62 README.md
4a29871af667e66ccf5239ca8e53bec4347b4e4a 
0143c9d92060c0c529a4346333433559bf9a1fe0 README.md
```

Y si contamos las líneas, se observa que hay 6 objetos. 

Y después con el comando `git cat-file -t {hash}`, se puede ver qué tipo de objeto es.

![](photos/2026-01-13%2011-19-35.png)
 
Por lo tanto podemos ver que se han hecho 2 commits(`Initial commit` y `Update README.md`), que cada uno de ellos apunta a un blob distinto (dos versiones del `README.md`).

Además, según el output del comando original, se han comprimido los archivos en un solo archivo que descargar. 
```sh
Total 6 (delta 0), reused 0 (delta 0), pack-reused 5 (from 1)
```

### Uso del comando `git status`

Este comando sirve para recibir un informe indicando qué está ocurriendo en la carpeta. Dice qué archivos han sido cambiados, cuáles son nuevos y qué se va a guardar en el siguiente commit del proyecto.

![](photos/2026-01-13%2011-09-59.png)

Según el output del comando vemos que estamos en la rama llamada `main`, y que está sincronizada correctamente con la rama que está subida en el repositorio remoto `origin/main`.
También, nos informa de que hay archivos sin "trackear", lo que quiere decir que git no está "vigilando" esos archivos. Por lo que git no mantendría el historial de los archivos que no estén trackeados. Para hacer que empiece a tenerlos en cuenta hay que usar el comando `git add`, mediante el cuál "prepararía" la carpeta `p1` para poderla subir en el siguiente commit. 


### Uso del comando `git add .`
El comando `git add .` añade todos los archivos y carpetas que han sido modificados y que no están en el archivo `.gitignore` a la parte de "staging", en este momento, los archivos ya están siendo vigilados por git y en el momento que haga un `git commit`, se almacenará el estado actual de los archivos trackeados. Por lo tanto, serviría para hacer un histórico de los archivos guardados en la carpeta `p1`.

![](photos/2026-01-13%2011-28-25.png)

Además, según el output del comando, somos informados de que se está metiendo un repositorio dentro del mismo repositorio, esto se debe a que la carpeta `p1` contiene la información de un histórico de git, con su propia carpeta oculta `.git`.
Por lo tanto, el repositorio principal `p1-fork` no está almacenando los archivos de dentro de la carpeta `p1`. Y que para arreglarlo deberíamos eliminar la carpeta `.git` de dentro de la carpeta `p1` y volver a hacer `git add .` o usar otros comandos más avanzados como el `submodule`.
 
Para hacerlo, he optado por eliminar la carpeta `.git` mediante el uso del comando `rm -rf p1/.git/`

Después borro el caché que ha creado de la carpeta `p1` mediante `git rm --cached -f p1`

```sh
@Tatuck ➜ /workspaces/p1-fork (main) $ git rm --cached -f p1
rm 'p1'
```

Y ahora ya puedo ejecutar `git add .`

```sh
@Tatuck ➜ /workspaces/p1-fork (main) $ git add .
```

Y vemos que ahora ya no devuelve nada.
Si volvemos a ejecutar `git status`, vemos como ha detectado el nuevo archivo `p1/README.md` y que está en la etapa de staging, por lo que en el siguiente commit, guardará la carpeta `p1` con el archivo `README.md` en su interior.
 
![](photos/2026-01-13%2011-38-51.png)

### Uso del comando `git commit`
Mediante el comando `git commit -m "Subida carpeta p1"`, se puede crear un commit con los nuevos cambios en la etapa de staging en el repositorio local. Poniendo como mensaje corto "Subida carpeta p1"

![](photos/2026-01-13%2011-40-16.png)

El output del comando nos indica que ha creado el nuevo commit en la rama `main` con el nombre `Subida carpeta p1` y que contiene un nuevo archivo `README.md` con 24 inserciones, lo que quiere decir que el archivo nuevo tiene 24 nuevas líneas de texto.
Un commit es como una "foto" de los cambios en el historial local del repositorio en el ordenador. 

### Uso del comando `git push`
El comando `git push` sirve para publicar los nuevos commits, al repositorio remoto, en este caso hospedado en mi cuenta de Github, [https://github.com/Tatuck/p1-fork](https://github.com/Tatuck/p1-fork).

![](photos/2026-01-13%2011-44-55.png)

Según el output del comando, se han subido 3 objetos nuevos, los cuales los ha comprimido en 2 paquetes de información. El comando ha subido todos los cambios de la rama `main` en local a la rama `main` en remoto. Esto se debe a que git sabe que la rama `main` está vinculada con la rama `origin/main`, en Github, por lo que al poner `git push`, git asume que la quiero subir a la rama llamada `main` en remoto sin yo tener que especificar nada.
Ahora el texto nuevo está subido a Github y otra persona que haga `git pull` podría traerse los cambios para poder visualizarlo o editarlo en su propia máquina.

### Uso del comando `git checkout -b`
El commando `git checkout -b feature/1` crea una nueva rama llamada `feature/1` y cambia el puntero `HEAD` a ella. Este tipo de ramas se utilizan para añadir algo de funcionalidad en el repositorio actual.

![](photos/2026-01-13%2011-51-08.png)

Según el output, se ha creado la nueva rama y se ha cambiado a ella. Por lo tanto, todos los cambios que haga en esta rama estarán aislados de la rama `main`. Una vez esté contento con los cambios, debería hacer un `merge` para poder unir la rama `main` con la nueva rama `feature/1`, teniendo que solucionar los posibles conflictos en el código como por ejemplo, que otra persona haya modificado `main` justo en la parte que yo he editado.

### Uso del comando `git checkout`
El comando `git checkout main` permite poder moverse a otra rama. En este caso serviría para movernos a la rama `main`.

![](photos/2026-01-13%2011-54-35.png)

En el output del comando se indica que no tiene cambios con la rama subida en remoto, `origin/main`.

### Información extra
Ahora voy a aprovechar la nueva rama `feature/1` para editar `README.md` y poder documentar los cambios que he hecho en este repositorio. Además de mostrar el entorno de desarrollo que tengo en el ordenador.

Primero, me muevo a la rama `feature/1`
 
Y ahora edito el archivo `README.md` con el contenido del archivo que actualmente estás leyendo.

Una vez tengo el archivo terminado, ejecutaré los comandos siguientes
```sh
git add .
git commit
```
Este último comando me abrirá una ventana donde podré escribir un mensaje largo de commit, según el formato utilizado, la primera línea será el título del commit y las siguientes serán la descripción del mismo.

Después me iré a la rama `main` mediante `git checkout main` y desde ahí ejecutaré el comando `git merge feature/1` para poder hacer el merge. Posteriormente, ejecutaré `git push` para publicar la rama `main` con los nuevos cambios al repositorio remoto hosteado en Github.

![](photos/2026-01-13%2012-57-06.png)

## Documentación del entorno de desarrollo Java
En esta sección voy a dejar evidencias de que tengo correctamente instalado el entorno de desarrollo exigido en el enunciado de la práctica, incluyendo los siguientes programas.
```
Java
Maven
Editor de código (IntelliJ + VSCode)
```

### Java
![](photos/2026-01-13%2012-55-22.png)

### Maven
![](photos/2026-01-13%2012-40-15.png)

### IntelliJ
![](photos/2026-01-13%2012-41-24.png)

### VSCode
![](photos/2026-01-13%2012-42-10.png)