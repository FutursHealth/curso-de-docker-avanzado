# 💻 Bienvenido/a al curso de Docker Avanzado
En este documento tendréis a vuestra disposición una serie de herramientas, enlaces de interés, comandos, así como los ejemplos que se irán mostrando durante el curso para que podáis ponerlo en práctica desde vuestros equipos.

# 💡 Antes de empezar ...  
A continuación os indicamos la URL del curso anterior, donde encontraréis una serie de herramientas que recomendamos tener instaladas para la realización del curso. Recordamos que la única estrictamente necesaria sería Docker.

En la URL también encontraréis los comandos de Docker más comunes, así como las tips y las ayudas para la realización de los ejercicios más básicos.

URL del curso [Docker from Zero to Hero](https://github.com/FutursHealth/curso-de-docker)

# 📚 Ejercicios  
A continuación os proponemos una serie de ejercicios que se irán resolviendo durante la clase. En ellos podrás poner en práctica todos los conceptos teóricos vistos durante la misma, así como repasar todos los comandos y pasos que se realizarán para resolver cada uno de ellos.  
Para ello será necesario clonarse o bajarse el repositorio que compartimos para este curso en [GitHub](https://github.com/FutursHealth/curso-de-docker-avanzado)

## Clonar un repositorio Git

Para clonar el repositorio de este curso, utilizaremos Git para descargar los archivos a tu máquina local. Si no tienes instalado Git en tu equipo y quieres hacerlo, [lo explicamos al inicio de este documento](https://github.com/FutursHealth/curso-de-docker/edit/main/README.md#%EF%B8%8F-instala-git).
En caso de **no tener Git instalado** también puedes descargarte el repositorio directamente [desde este link](https://github.com/FutursHealth/curso-de-docker-avanzado/archive/refs/heads/main.zip) y descomprimirlo en la ruta que desees, por ejemplo `C:/Curso_Docker_Avanzado/`.  

**Instrucciones:** 

1. Si has instalado o tienes instalado Git en tu máquina, abre una terminal (Linux/Mac) o PowerShell (Windows).  
2. Ves hasta la ruta donde desees descargar los archivos.  
3. Ejecuta el siguiente comando para clonar el repositorio:  
    ```bash
    git clone https://github.com/FutursHealth/curso-de-docker-avanzado
    ```

  Esto descargará los archivos del proyecto en tu directorio actual.

# 📗 Ejercicio 1: Variables de entorno
En este ejercicio práctico aprenderás a usar las variables de entorno en Docker. Veremos además, como consultar las variables que tiene nuestro contenedor, tanto las que nosotros le indiquemos como las que vengan de base en la imagen.

### 1. Inspeccionar imagen ngninx
Comenzaremos realizando una búsqueda de la imagen que vamos a usar durante el curso. Se trata de una imagen [nginx](https://hub.docker.com/_/nginx). Dentro de la referencia de esta imagen, podemos ver la descripción de la imagen, como usarla, etc., y en la parte de tags podemos ver las distintas versiones que hay para esta imagen. Como nosotros queremos que sea una imagen liviana, nos centraremos en usar la imagen de nginx basada en alpine.

### 2. Identificarnos en el registro
Para poder trabajar durante el curso con nuestro repositorio de Docker HUB, lo primero que haremos será identificarnos en el repositorio. Como las credenciales se guardan en cache, quedaremos logeados para el resto de sesiones.

- Regístrate con tu usuario y contraseña de Docker Hub  
`docker login docker.io -u cursodockerfuturs`
- Accede usando la contraseña proporcionada

### 3. Levantar el contenedor
Primero levantaremos el contenedor como veníamos haciendo hasta ahora:
- `docker run -d --name web-ngninx -p 81:80 nginx:alpine`  

  > 💡**RECORDATORIO**
  > - `-d` para correr en segundo plano
  > - Importante darle nombre al contenedor
  > - No puedo levantar contenedores que usen el mismo nombre que otro, aunque esté muerto
  > - No puedo levantar contenedores que usen el mismo puerto que otro que esté levantado

Accedemos a nuestra URL http://localhost:81 para comprobar nuestra aplicación web.

Ahora vamos a levantar de nuevo el contenedor, pero asignándole una variable de entorno:  
`docker run -d --name web-ngninx -e PRUEBA=1234 -p 81:80 nginx:alpine`

>*¿No has podido?*

Accedemos de nuevo a nuestra URL http://localhost:8X para comprobar nuestra aplicación web.

Para poder consultar las variables de entorno que tenemos en nuestro contenedor, tenemos varias opciones:

**Opción 1**  
Entramos al contenedor que hemos creado y mostramos las variables:
- `docker exec -it web-ngninx2 sh`
- `echo $PRUEBA`
- `printenv`

**Opción 2**  
Inspeccionamos el contenedor docker que hemos creado con el comando `docker inspect web-ngninx2`.  
Con esta opción además podremos ver mucha más información útil como el ID, argumentos que tiene, el estado del contenedor, la imagen, el puerto, los volúmenes, variables de entorno, networking, etc...

Una vez que veamos que está correcta podemos eliminar los contenedores:  
- `docker rm -f web-nginx web-ngninx2`

> [!Note]
> - `-f` para forzar. De esta forma evito tener que parar previamente el contenedor.

# 📗 Ejercicio 2: Volúmenes Docker
En este ejercicio práctico aprenderás a usar los volúmenes en Docker. Veremos las dos opciones disponibles: Docker Volume y Bind Mount.
Al igual que en el ejercicio anterior, si no conocemos el contenedor que vamos a ejecutar, vamos a la documentación e identificamos los volúmenes que usa.
También aprenderemos a copiar archivos de mi local al contenedor y viceversa.

### 1. Inspeccionar imagen ngninx
Inspecciona la imagen [nginx](https://hub.docker.com/_/nginx) para determinar cuál es la ruta donde guarda los datos estáticos. Esa ruta será la que nosotros usemos como volumen.

### 2. Levantar contenedor docker
Ahora levantaremos un contenedor docker como hemos hecho en el ejercicio anterior:  
- `docker run -d --name web-nginx -p 81:80 nginx:alpine`

Comprobamos que se levanta la aplicación correctamente en http://localhost:81

### 3. Editar aplicación
Ahora entraremos dentro del contenedor que hemos creado para personalizar los datos de mi aplicación.

- `docker exec -it web-nginx sh`
- `cd /usr/share/ngninx/html`
- `ls`
- `echo "<h1>HOLA MUNDO</h1>" > index.html`

Podemos recargar de nuevo nuestra aplicación para comprobar que se ha editado correctamente: http://localhost:81

### 4. Terminar aplicación
Ahora vamos a salir del contenedor y a borrarlo para crearlo de nuevo. (Paso 2)

**¿Qué ha pasado?**

En este punto vemos la importancia de la persistencia de datos. Es por eso que en Docker solemos trabajar con volúmenes.

### 5. Crear Docker volume
Primero vamos a crear un docker volume (host) y lo asociaremos a nuestra aplicación a la hora de crear el contenedor de nuevo.

1. `docker volume create web`
2. `docker volume web ls`
3. Creamos de nuevo el contenedor:  
`docker run -d --name web-nginx -v web:/usr/share/nginx/html -p 81:80 nginx:alpine`
4. Inspeccionamos la imagen para ver la parte **Mounts**: `docker inspect web-nginx`

### 6. Creamos un archivo local
Ahora vamos a crear un archivo en local **index.html** y lo vamos a copiar dentro de la ruta del contenedor.

1. `echo "<h1>hola desde contenedor</h1>" >index.html`
2. `docker cp index.html web-ngninx:/usr/share/nginx/html`

Comprobamos URL http://localhost:81 y repetimos paso 4.

### Opcional
Puedes hacer la prueba de crear un nuevo contenedor (usando otro nombre y otro puerto) usando el mismo volumen. Podrás comprobar que la aplicación que se levanta tiene directamente la información que hemos añadido previamente.

Si no vamos a usar más el volumen y queremos borrarlo: `docker volume rm web`

### 7. Bind Mount
Para usar la opción de Bind Mount, vamos a crear una carpeta que se llame **static** en nuestra ruta actual. Dentro de esa ruta copiaremos un nuevo archivo **index.html** donde editaremos la información que aparecerá en la web. Luego crearemos el contenedor mapeando la nueva ruta e inspeccionaremos el tipo de volumen que aparece en el contenedor.

1. `mkdir static`
2. `echo "<h1>hola desde Bind Mount</h1>" >static/index.html`
3. `docker run -d --name web-nginx -v $PWD/static:/usr/share/nginx/html -p 81:80 nginx:alpine`
4. `docker inspect web-ngninx`

> [!Note]
> Si queremos proteger los archivos del volumen para que sean solo lectura añadimos :ro al final:  
> `docker run -d --name web-nginx -v $PWD/static:/usr/share/nginx/html:ro -p 81:80 nginx:alpine`

# 📗 Ejercicio 3: Redes Docker
En este ejercicio práctico aprenderás a usar las redes en Docker. Veremos los tipos de redes que hay, y su importancia para la conexión de los contenedores.

### 1. Creación de red 
Antes de crear nuestra propia red en Docker, vamos a echar un vistazo a lo que tenemos instalado por defecto. Para ello ejecutaremos el siguiente comando:  
`docker network ls`

Aquí podemos ver los 3 tipos de red que hay por defecto:
- **Bridge:** la red por defecto. Se usa para que los contenedores puedan comunicarse. Se usa con mapeo de puertos (`-p`)
- **Host:** la red del host directamente. No asigna IP al contenedor. No hay que redireccionar puertos.
- **None:** el contenedor no tendría acceso a ninguna red. Para tareas seguras donde no necesitamos comunicación externa.

Ahora si, crearemos nuestra primera red de docker:  
- `docker network create prueba-red`
- `docker network ls`

### 2. Levantar aplicación
Para este ejercicio hemos creado una imagen basada en un guestbook. Vamos a levantar una instancia de la aplicación para ver cómo funciona:  
`docker run -d --name guest -p 8081:5000 cursodockerfuturs/guestbook:1.0.0`

Comprobamos que la aplicación se ha levantado correctamente en la URL http://localhost:8081

### 3. Levantar redis
Como hemos visto, la aplicación se queda esperando una conexión. Para ello vamos a hacer uso de la herramienta Redis, con una imagen liviana basada en alpine. El puerto por defecto de la aplicación de Redis es el **6379**, pero como nosotros no necesitamos acceder, no mapearemos ningún puerto:  
`docker run -d --name redis redis:alpine`

Comprobamos de nuevo la aplicación para ver si engancha correctamente el Redis.

**¿Qué ha pasado?**

Vamos a eliminar los contenedores previamente creados para poder crearlos de nuevo correctamente:  
`docker rm -f redis guest`

### 4. Levantar aplicación y redis bajo misma red
De nuevo, vamos a repetir los pasos 2 y 3, pero esta vez usando la red que hemos creado previamente en el paso 1.  
Para ello primero levantaremos la aplicación:  
`docker run -d --name guest --network prueba-red -p 8081:5000 cursodockerfuturs/guestbook:1.0.0`

Comprobamos que la aplicación se ha levantado correctamente en la URL http://localhost:8081 y que está esperando una conexión.  

Levantamos ahora nuestro servicio Redis bajo la misma red:  
`docker run -d --name redis --network prueba-red redis:alpine`

Comprobamos de nuevo la aplicación y vemos que la conexión ya se ha establecido.

### Opcional
Podemos entrar dentro del contenedor de redis para monitorear los logs cada vez que escribamos algo en nuestro guestbook:  
- `docker exec -it redis redis-cli`  
- `monitor`

# Ejercicio 4: Mi primer Docker Compose
En este ejercicio crearemos nuestro primer Docker Compose en el que levantaremos los 2 servicios usados en el ejercicio anterior con un solo comando.  
Deberemos especificar los servicios, los nombres de contenedor, las imágenes, los puertos, la red, etc.  

Para ello deberemos conocer la estructura de un **docker-compose.yml**:
```docker-compose.yml
services:
  service_name:
    container_name: name
    image: image
    ports:
      - xxxx:xxxx
    networks:
      - name
    depends_on:
      - name

  another_service_name:
    container_name: name
    image: image
    networks:
      - name

networks:
  name:
    driver: bridge
```

Cuando tengas terminado el archivo **docker-compose.yml**, sitúate en el directorio donde lo tengas guardado y lanza el comando:  
`docker compose up`

# Ejercicio 5: creando mi herramienta de análisis de código
En este ejercicio crearemos un servicio de SonarQube para análisis de código. Este servicio necesita una base de datos para funcionar. Es por ello que ahora vamos a poner en práctica todo lo aprendido anteriormente.  
Necesitamos crear un **docker-compose.yaml** que contenga los dos servicios, bajo la misma red, y usando los volúmenes necesarios. Deberemos seguir la misma estructura que en el ejercicio anterior.  
Las imágenes que usaremos son las siguientes:
1. postgres
2. sonarqube

### Paso 1: Inspeccionar imágenes
Inspecciona las imágenes en Docker Hub para averiguar lo necesario de dichas imágenes.

### Paso 2: Crea tu docker-compose.yml
Del mismo modo que en el ejercicio anterior, crea tu archivo **docker-compose.yml** y cuando creas que lo tienes terminado, lanza el comando:  
`docker-compose up`

# Ejercicio 6: Voting-app
En este último ejercicio vamos a crear una aplicación de votos. Para ello crearemos un **docker-compose.yml** con varios servicios, volúmenes, redes, etc., que contenga todo lo visto anteriormente. Las imágenes que vamos a usar son las siguientes:  
1. postgres
2. redis
3. dpage/pgadmin4
4. cursodockerfuturs/voting-app

### Datos necesarios
Las variables de entorno para pgadmin son las siguientes:  
```bash
PGADMIN_DEFAULT_EMAIL: "prueba@gmail.com"
PGADMIN_DEFAULT_PASSWORD: "prueba1234"
```

### Uso de pgadmin

1. Nos logeamos con las credenciales que hemos definido en el docker-compose.yml
2. Click derecho en Servers > Register > Server
3. Podemos darle el nombre que queramos
4. En la parte de connections:
  - Host name/address podemos usar el nombre del contenedor de postgres (db)
  - Maintenance database: el que hayamos definido en el docker-compose.yml (postgres)
  - Username: el que hayamos definido en el docker-compose.yml (postgres)
  - Password: el que hayamos definido en el docker-compose.yml (postgres)
5. Abrimos el servidor > Databases > postgres > Schemas > Tables > click derecho en *votes* > *View/Edit Data* > *First 100 rows*
6. Podemos ver la información de las columnas