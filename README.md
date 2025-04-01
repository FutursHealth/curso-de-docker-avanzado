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

## Comandos generales
### Variables de entorno
| Comando | Descripción |
|-------------------|-------------------|
| `docker run -e USUARIO=prueba ubuntu` | Tag -e o --env para declarar una variable de entorno al crear un contenedor |
### Docker Volume
| Comando | Descripción |
|-------------------|-------------------|
| `docker volume create <name>` | Crea un volumen con el nombre indicado |
| `docker volume rm <name>` | Elimina el volumen indicado (si no está siendo usado)|
| `docker volume prune` | Elimina los volumenes que no están siendo usados por ningún contenedor |
| `docker volume ls` | Lista los volumenes creados con información adicional |
| `docker volume inspect <name>` | Nos dará información detallada del volumen indicado |
### Docker Network
| Comando | Descripción |
|-------------------|-------------------|
| `docker network create <name>` | Crea una red con el nombre indicado y driver por defecto (bridge) |
| `docker network rm <name>` | Elimina la red indicada (si no está siendo usada) |
| `docker network prune` | Elimina las redes que no están siendo usados por ningún contenedor |
| `docker netowrk ls` | Lista las redes creadas con información adicional |
| `docker network inspect <name>` | Nos dará información detallada de la red indicada |
### Docker Compose
| Comando | Descripción |
|-------------------|-------------------|
| `docker-compose up` | Crea los contenedores descritos en el docker-compose.yml |
| `docker-compose up -d` | Crea los contenedores descritos en el docker-compose.yml en segundo plano (sin mostrar logs) |
| `docker-compose stop` | Detiene los contenedores previamente levantados |
| `docker-compose build` | Ejecuta (si está definido) el proceso de construcción de una imagen que vaya a ser usada en mi docker-compose.yml a partir de los ficheros Dockerfile que le indique |

# 📗 1 - Ejercicio Variables de entorno
En este ejercicio práctico aprenderás a usar las variables de entorno en Docker. Veremos además, como consultar las variables que tiene nuestro contenedor, tanto las que nosotros le indiquemos como las que vengan de base en la imagen.

**1. Inspeccionar imagen nginx**  
Comenzaremos realizando una búsqueda de la imagen que vamos a usar durante el curso. Se trata de una imagen [nginx](https://hub.docker.com/_/nginx). Dentro de la referencia de esta imagen, podemos ver la descripción de la imagen, como usarla, etc., y en la parte de tags podemos ver las distintas versiones que hay para esta imagen. Como nosotros queremos que sea una imagen liviana, nos centraremos en usar la imagen de nginx basada en alpine.

**2. Identificarnos en el registro**  
Para poder trabajar durante el curso con nuestro repositorio de Docker HUB, lo primero que haremos será identificarnos en el repositorio. Como las credenciales se guardan en cache, quedaremos logeados para el resto de sesiones.

- Regístrate con tu usuario y contraseña de Docker Hub  
`docker login docker.io -u cursodockerfuturs`
- Accede usando la contraseña proporcionada

**3. Levantar el contenedor**  
Primero levantaremos el contenedor como veníamos haciendo hasta ahora:
- `docker run -d --name web-nginx -p 81:80 nginx:alpine`  

  > 💡**RECORDATORIO**
  > - `-d` para correr en segundo plano
  > - Importante darle nombre al contenedor
  > - No puedo levantar contenedores que usen el mismo nombre que otro, aunque esté muerto
  > - No puedo levantar contenedores que usen el mismo puerto que otro que esté levantado

Accedemos a nuestra URL http://localhost:81 para comprobar nuestra aplicación web.

Ahora vamos a levantar de nuevo el contenedor, pero asignándole una variable de entorno:  
`docker run -d --name web-nginx -e PRUEBA=1234 -p 81:80 nginx:alpine`

>*¿No has podido?*

Accedemos de nuevo a nuestra URL http://localhost:8X para comprobar nuestra aplicación web.

Para poder consultar las variables de entorno que tenemos en nuestro contenedor, tenemos varias opciones:

**Opción 1**  
Entramos al contenedor que hemos creado y mostramos las variables:
- `docker exec -it web-nginx2 sh`
- `echo $PRUEBA`
- `printenv`

**Opción 2**  
Inspeccionamos el contenedor docker que hemos creado con el comando `docker inspect web-nginx2`.  
Con esta opción además podremos ver mucha más información útil como el ID, argumentos que tiene, el estado del contenedor, la imagen, el puerto, los volúmenes, variables de entorno, networking, etc...

Una vez que veamos que está correcta podemos eliminar los contenedores:  
- `docker rm -f web-nginx web-nginx2`

> [!Note]
> - `-f` para forzar. De esta forma evito tener que parar previamente el contenedor.

# 📗 2 - Ejercicio Volúmenes Docker  
En este ejercicio práctico aprenderás a usar los volúmenes en Docker. Veremos las dos opciones disponibles: Docker Volume y Bind Mount.
Al igual que en el ejercicio anterior, si no conocemos el contenedor que vamos a ejecutar, vamos a la documentación e identificamos los volúmenes que usa.
También aprenderemos a copiar archivos de mi local al contenedor y viceversa.

**1. Inspeccionar imagen nginx**  
Inspecciona la imagen [nginx](https://hub.docker.com/_/nginx) para determinar cuál es la ruta donde guarda los datos estáticos. Esa ruta será la que nosotros usemos como volumen.

**2. Levantar contenedor docker**  
Ahora levantaremos un contenedor docker como hemos hecho en el ejercicio anterior:  
- `docker run -d --name web-nginx -p 81:80 nginx:alpine`

Comprobamos que se levanta la aplicación correctamente en http://localhost:81

**3. Editar aplicación**  
Ahora entraremos dentro del contenedor que hemos creado para personalizar los datos de mi aplicación.

- `docker exec -it web-nginx sh`
- `cd /usr/share/nginx/html`
- `ls`
- `echo "<h1>HOLA MUNDO</h1>" > index.html`

Podemos recargar de nuevo nuestra aplicación para comprobar que se ha editado correctamente: http://localhost:81

**4. Terminar aplicación**  
Ahora vamos a salir del contenedor y a borrarlo para crearlo de nuevo. (Paso 2)

   **¿Qué ha pasado?**  
   En este punto vemos la importancia de la persistencia de datos. Es por eso que en Docker solemos trabajar con volúmenes.

**5. Crear Docker volume**  
Primero vamos a crear un docker volume (host) y lo asociaremos a nuestra aplicación a la hora de crear el contenedor de nuevo.

1. `docker volume create web`
2. `docker volume ls`
3. Creamos de nuevo el contenedor:  
`docker run -d --name web-nginx -v web:/usr/share/nginx/html -p 81:80 nginx:alpine`
4. Inspeccionamos la imagen para ver la parte **Mounts**: `docker inspect web-nginx`

**6. Creamos un archivo local**  
Ahora vamos a crear un archivo en local **index.html** y lo vamos a copiar dentro de la ruta del contenedor.

1. `echo "<h1>hola desde contenedor</h1>" >index.html`
2. `docker cp index.html web-nginx:/usr/share/nginx/html`

Comprobamos URL `http://localhost:81` y repetimos paso 4.

**Opcional**  
Puedes hacer la prueba de crear un nuevo contenedor (usando otro nombre y otro puerto) usando el mismo volumen. Podrás comprobar que la aplicación que se levanta tiene directamente la información que hemos añadido previamente.

Si no vamos a usar más el volumen y queremos borrarlo: `docker volume rm web`

**7. Bind Mount**  
Para usar la opción de Bind Mount, vamos a crear una carpeta que se llame **static** en nuestra ruta actual. Dentro de esa ruta copiaremos un nuevo archivo **index.html** donde editaremos la información que aparecerá en la web. Luego crearemos el contenedor mapeando la nueva ruta e inspeccionaremos el tipo de volumen que aparece en el contenedor.  

1. `mkdir static`
2. `echo "<h1>hola desde Bind Mount</h1>" >static/index.html`
3. `docker run -d --name web-nginx -v $PWD/static:/usr/share/nginx/html -p 81:80 nginx:alpine`
4. `docker inspect web-nginx`

> [!Note]
> Si queremos proteger los archivos del volumen para que sean solo lectura añadimos `:ro` al final:  
> `docker run -d --name web-nginx -v $PWD/static:/usr/share/nginx/html:ro -p 81:80 nginx:alpine`

# 📗 3 - Ejercicio Redes Docker
En este ejercicio práctico aprenderás a usar las redes en Docker. Veremos los tipos de redes que hay, y su importancia para la conexión de los contenedores.

**1. Creación de red**  
Antes de crear nuestra propia red en Docker, vamos a echar un vistazo a lo que tenemos instalado por defecto. Para ello ejecutaremos el siguiente comando:  
`docker network ls`

   Aquí podemos ver los 3 tipos de red que hay por defecto:
   - **Bridge:** la red por defecto. Se usa para que los contenedores puedan comunicarse. Se usa con mapeo de puertos (`-p`)
   - **Host:** la red del host directamente. No asigna IP al contenedor. No hay que redireccionar puertos.
   - **None:** el contenedor no tendría acceso a ninguna red. Para tareas seguras donde no necesitamos comunicación externa.

   Ahora si, crearemos nuestra primera red de docker:  
   - `docker network create prueba-red`
   - `docker network ls`

**2. Levantar aplicación**  
Para este ejercicio hemos creado una imagen basada en un guestbook. Vamos a levantar una instancia de la aplicación para ver cómo funciona:  
`docker run -d --name guest -p 8081:5000 cursodockerfuturs/guestbook:1.0.0`

Comprobamos que la aplicación se ha levantado correctamente en la URL http://localhost:8081

**3. Levantar redis**  
Como hemos visto, la aplicación se queda esperando una conexión. Para ello vamos a hacer uso de la herramienta Redis, con una imagen liviana basada en alpine. El puerto por defecto de la aplicación de Redis es el **6379**, pero como nosotros no necesitamos acceder, no mapearemos ningún puerto:  
`docker run -d --name redis redis:alpine`

Comprobamos de nuevo la aplicación para ver si engancha correctamente el Redis.

   **¿Qué ha pasado?**  
   Vamos a eliminar los contenedores previamente creados para poder crearlos de nuevo correctamente:  
   `docker rm -f redis guest`

**4. Levantar aplicación y redis bajo misma red**  
De nuevo, vamos a repetir los pasos 2 y 3, pero esta vez usando la red que hemos creado previamente en el paso 1.  
Para ello primero levantaremos la aplicación:  
`docker run -d --name guest --network prueba-red -p 8081:5000 cursodockerfuturs/guestbook:1.0.0`

Comprobamos que la aplicación se ha levantado correctamente en la URL http://localhost:8081 y que está esperando una conexión.  

Levantamos ahora nuestro servicio Redis bajo la misma red:  
`docker run -d --name redis --network prueba-red redis:alpine`

Comprobamos de nuevo la aplicación y vemos que la conexión ya se ha establecido.

**Opcional**  
Podemos entrar dentro del contenedor de redis para monitorear los logs cada vez que escribamos algo en nuestro guestbook:  
- `docker exec -it redis redis-cli`  
- `monitor`

# 📚 4 - Ejercicios con Docker Compose
## 📗 4.1 - Mi primer Docker Compose
En este ejercicio crearemos nuestro primer Docker Compose en el que levantaremos los 2 servicios usados en el ejercicio anterior con un solo comando.  
Deberemos especificar los servicios, los nombres de contenedor, las imágenes, los puertos, la red, etc.  

Para ello deberemos conocer la estructura de un **docker-compose.yml**:
```dockerfile
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
`docker-compose up`

## 📗 4.2 - Creando mi herramienta de análisis de código
En este ejercicio crearemos un servicio de SonarQube para análisis de código. Este servicio necesita una base de datos para funcionar. Es por ello que ahora vamos a poner en práctica todo lo aprendido anteriormente.  
Necesitamos crear un **docker-compose.yaml** que contenga los dos servicios, bajo la misma red, y usando los volúmenes necesarios. Deberemos seguir la misma estructura que en el ejercicio anterior.  
Las imágenes que usaremos son las siguientes:
1. postgres
2. sonarqube

**Paso 1: Inspeccionar imágenes**  
Inspecciona las imágenes en Docker Hub para averiguar lo necesario de dichas imágenes.

**Paso 2: Crea tu `docker-compose.yml`**  
Del mismo modo que en el ejercicio anterior, crea tu archivo `docker-compose.yml` y cuando creas que lo tienes terminado, lanza el comando:  
`docker-compose up`

**Datos necesarios**  
Las variables de entorno para sonarqube son las siguientes:  
```bash
SONAR_JDBC_URL: jdbc:postgresql://db:5432/postgres
SONAR_JDBC_USERNAME: postgres
SONAR_JDBC_PASSWORD: postgres
```

> [!Important]
> La primera vez que accedemos al portal de Sonarqube nos pedirá usuario y contraseña. Por defecto son:   
> - User: `admin`  Password: `admin`  
> Una vez que hayáis accedido, os pedirá que la cambiéis. Podéis poner la misma otra vez o una nueva.

## 📗 4.3 - Voting-app
En este último ejercicio vamos a crear una aplicación de votos. Para ello crearemos un **docker-compose.yml** con varios servicios, volúmenes, redes, etc., que contenga todo lo visto anteriormente.  
    
El diagrama de la aplicación es el siguiente:  
   
![Voting_app](/0.0_resources/voting-app.drawio.png)

Las imágenes que vamos a usar son las siguientes:  
1. postgres
2. redis
3. dpage/pgadmin4 (opcional)
4. cursodockerfuturs/voting-app:worker-1.0.0
5. cursodockerfuturs/voting-app:result-2.0.0
6. cursodockerfuturs/voting-app:vote-2.0.0

> [!Important]
> Las imágenes de Voting App están diseñadas de tal manera que espera nombres de servicio exactos.  
> Para que funcione correctamente, los nombres de los servicios de Redis y Postgres tienen que llamarse **redis** y **db** obligatoriamente.

**Datos necesarios para PGadmin**  
Las variables de entorno para pgadmin son las siguientes:  
```bash
PGADMIN_DEFAULT_EMAIL: "prueba@gmail.com"
PGADMIN_DEFAULT_PASSWORD: "prueba1234"
```

**Uso de pgadmin**  
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

# 📚 5 - Seguridad  
## 📗 5.1 - Aislamiento de Namespaces en Docker  
Con este ejercicio vamos a entender cómo Docker usa namespaces para aislar procesos y recursos entre contenedores y el host.  
Los *namespaces* en Docker permiten aislar diferentes aspectos del sistema para proporcionar entornos independientes y seguros para los contenedores. A continuación, se detallan los principales *namespaces* y su función dentro de Docker:  

| Namespace | Función | Ejemplo en Docker |
|-----------|---------|------------------|
| **PID** | Aisla procesos entre el contenedor y el host. | Un contenedor no puede ver ni gestionar procesos del host. |
| **Mount** | Aisla el sistema de archivos. | Cada contenedor tiene su propia vista del sistema de archivos. |
| **Network** | Aisla interfaces de red. | Cada contenedor tiene su propia IP y configuración de red. |
| **User** | Aisla los usuarios dentro del contenedor. | Permite mapear usuarios del contenedor a usuarios sin privilegios en el host. |
| **UTS** | Aisla nombres de host. | Un contenedor puede tener su propio hostname sin afectar al host. |
| **IPC** | Aisla la memoria compartida entre procesos. | Un contenedor no puede acceder a la memoria compartida de otro. |

Cada uno de estos *namespaces* contribuye a la seguridad y aislamiento de los contenedores en Docker, permitiendo ejecutar múltiples aplicaciones en un mismo host sin interferencias entre ellas.  

**1. Ejecutamos un contenedor en segundo plano con un proceso "infinito"**  
```bash
docker run -d --name test-namespaces ubuntu sleep 600
```
**2. Vemos los procesos dentro del contenedor**  
```bash
docker exec -it test-namespaces ps aux
```
**3. Intenemos encontrar ese proceso en el host**  
```bash
Get-Process | Where-Object {$_.ProcessName -like "*sleep*"}
```
⚠️ **El proceso NO aparecerá en el host**, porque está en un **namespace de PID separado**.  
  
**4. Verificamos el namespace del contenedor**  
```bash
docker inspect --format '{{ .State.Pid }}' test-namespaces
```
Esto devolverá el **PID en el host**, que es distinto del PID dentro del contenedor.  
**5. Ahora buscamos ese PID en el host (necesitaremos hacerlo a través de WSL)**  
```bash
wsl  
ps aux | grep <pid-paso4>
```  
Ahora podremos ver que efectivamente existe un PID de host reservado para la ejecución de los PIDs internos del contenedor.  

## 📗 5.2 - Análisis de Dockerfile con Hadolint  

Con este ejercicio vamos a detectar **malas prácticas** en la construcción de imágenes Docker.  
**1. Creamos un archivo Dockerfile con problemas de seguridad o usamos algunos que ya tengamos**  
```Dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y curl
CMD ["bash"]
```
**2. Ejecutamos Hadolint dentro de un contenedor**  
Powershell console
```pwsh
Get-Content Dockerfile | docker run --rm -i hadolint/hadolint
```  
WSL console
```bash
docker run --rm -i hadolint/hadolint < Dockerfile
```  
**3. Analizamos los resultados**  
* **DL3007 warning:** `FROM ubuntu:latest` usa `latest`, lo que puede generar inconsistencias cuando la imagen se actualiza.  
**Solución**: Especificar una versión concreta, como `ubuntu:22.04`.  
* **DL3008 warning:** `apt-get install` no especifica una versión exacta de los paquetes, lo que puede traer actualizaciones inesperadas.  
**Solución:** Definir una versión explícita con `apt-get install <package>=<version>`.  
* **DL3015 info:** `apt-get install` instala paquetes recomendados por defecto, lo que puede traer dependencias innecesarias.  
**Solución:** Agregar `--no-install-recommends` para evitar instalar paquetes no esenciales.  
* **DL3009 info:** No se eliminan las listas de paquetes después de la instalación, lo que aumenta el tamaño de la imagen.  
**Solución:** Ejecutar `rm -rf /var/lib/apt/lists/*` para limpiar archivos innecesarios.  
  
Con esta info ya podemos corregir nuestro Dockerfile aplicando todas las buenas prácticas. Quedando algo así:  
**📂 `/5.2_hadolint/`**  
```Dockerfile
FROM ubuntu:22.04

# Evitamos problemas de dependencias bloqueando la interacción del usuario
ARG DEBIAN_FRONTEND=noninteractive

# Instalamos paquetes con versiones fijas, sin recomendaciones adicionales y limpiando listas
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl=7.81.0-1ubuntu1.10 && \
    rm -rf /var/lib/apt/lists/*

# Usamos un usuario no root para mejorar la seguridad
RUN useradd -m appuser
USER appuser

CMD ["bash"]
```  

## 📗 5.3 - Análisis de Vulnerabilidades en Imágenes Docker con Trivy  
Con este ejercicio vamos a escanear una imagen Docker en busca de vulnerabilidades, este servicio lo ejecutaremos dentro de un contenedor.  
**1. Ejecutaremos Trivy desde un contenedor para analizar una imagen**  
```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nginx:latest
```
**2. Analizamos los resultados**  
* Se mostrarán vulnerabilidades detectadas, clasificadas por severidad.
* Si aparecen vulnerabilidades críticas, buscar una versión más segura de la imagen.

# 📚 6 - Monitoring & Logging  
## 📗 6.1 - Supervisar el uso de recursos de un contenedor con `docker stats`  
Con este ejercicio vamos a aprender a monitorear el consumo de CPU, memoria, red y disco de un contenedor en tiempo real.  

**1. Ejecutamos un contenedor con una carga artificial.**  
   ```powershell
   docker run -d --name test-monitoring ubuntu sh -c "while true; do echo 'Cargando CPU...'; done"
   ```  
**2. Ahora nos abrimos una nueva ventana de powershell y ejecutamos el siguiente comando:**  
   ```powershell
   docker stats test-monitoring
   ```
   Se mostrará información en tiempo real sobre el uso de **CPU, memoria, red y disco** del contenedor.  
**3. Por último, detenemos el contenedor y verificamos que desaparece del monitoreo:**   
   ```powershell
   docker rm -f test-monitoring
   ```
Como hemos podido comprobar, `docker stats` es útil para **detectar contenedores que consumen demasiados recursos**, lo que podría afectar al rendimiento del sistema.  

## 📗 6.2 - Monitoreo avanzado con cAdvisor  
Con este ejercicio vamos a aprender a ejecutar cAdvisor para obtener métricas detalladas sobre todos los contenedores.  
  
**1. Ejecutamos cAdvisor en un contenedor Docker**  
   ```powershell
   docker run -d --name=cadvisor -p 8080:8080 --volume=/var/run/docker.sock:/var/run/docker.sock gcr.io/cadvisor/cadvisor
   ```  
**2. Abrimos el navegador y accedemos a `http://localhost:8080`**  
   Se mostrarán gráficos con **CPU, memoria, red y disco** de todos los contenedores.  
   
**3. Probamos con otro contenedor para ver cómo cambian las métricas:**  
   ```powershell
   docker run -d --name stress-container ubuntu sh -c "while true; do echo 'Usando CPU'; done"
   ```
**4. Detenemos y eliminamos los contenedores**  
   ```powershell
   docker rm -f cadvisor stress-container
   ```

## 📗 6.3 - Gestión de logs en Docker  
Con este ejercicio aprenderemos a obtener y manejar logs de contenedores en ejecución.  
  
**1. Ejecutamos un contenedor que genera logs continuamente:**  
```powershell
docker run -d --name logger-container ubuntu sh -c "while true; do echo 'Log generado en: ' \$(date); sleep 2; done"
```  
**2. Vemos los logs en tiempo real:**  
```powershell
docker logs -f logger-container
```
💡 Explicación:
* `-f` permite seguir los logs en tiempo real, como `tail -f` en Linux.

**3. Detenemos el seguimiento de logs** (presionar `CTRL + C`).  
**4. Guardamos los logs en un archivo de texto:**  
```powershell
docker logs logger-container > logs.txt
```
**5. Eliminamos el contenedor:**  
```powershell
docker rm -f logger-container
```
Como podemos ver, Docker almacena logs por defecto en **JSON**, pero podemos acceder a ellos en tiempo real o guardarlos en archivos para análisis posterior.  

## 📗 6.4 - Centralización de logs con Fluentd   
Con este ejercicio aprenderemos a enviar logs de Docker a Fluentd para análisis centralizado.  

**1. Usaremos el archivo de configuración `fluent.conf`**  
**📂 `/6.4_fluentd/`**  
```bash
<source>
  @type forward
  port 24224
  bind 0.0.0.0
</source>

<filter **>
  @type record_transformer
  <record>
    container_name \${tag}
  </record>
</filter>

<match **>
  @type stdout
</match>
"@
```  
💡 Esto configura Fluentd para escuchar en el puerto `24224`, transforme los registros para incluir el nombre del contenedor y **muestre en consola** cualquier log (**) y lo **manda a la salida stándar (stdout)**.  

**2. Ejecutaremos un contenedor de Fluentd con archivo de configuración creado**  
```powershell
docker run -d --name fluentd -p 24224:24224 -v ${PWD}\6.4_fluentd:/fluentd/etc fluent/fluentd:v1.16-debian-2
```  
**3. Ejecutaremos un contenedor configurado para enviar logs a Fluentd:**  
```powershell
docker run -d --name app-logs --log-driver=fluentd --log-opt tag="app-container" --log-opt fluentd-address=localhost:24224 ubuntu sh -c "while true; do echo 'Log desde contenedor' ; sleep 2; done"
```
💡 **Esto enviará logs a Fluentd** en `localhost:24224`.  

**4. Comprobaremos que Fluentd está recibiendo los logs:**  
```powershell
docker logs fluentd
```
Si todo funciona bien, se verán los logs recibidos desde `app-logs`.  

**5. Detenemos y eliminamos los contenedores:**  
```powershell
docker rm -f fluentd
```  
⚠️ En este paso nos hemos encontrado que con Docker Desktop for Windows en muchas ocasiones se colgaba intentando parar el contenedor de `app-logs`, si se diera el caso, lo que tenemos que hacer es **reiniciar el engine** y ambos contenedores quedarán parados y listos para ser eliminados.  

![Restart_Engine](/0.0_resources/docker_desktop.png)  

# 📚 7 - Sistemas distribuidos y de clustering
En este último módulo del curso de **Docker Avanzado** hemos aprendido qué son los sitemas distribuidos y de clustering, cuáles son los mas famosos, y qué beneficios nos aportan a la hora de trabajar con contenedores.  
Dado que enseñar algún sistema como pueden ser **Service Fabric** o **Kubernetes** daría para otro curso entero, no queremos terminar este sin tener una primera aproximación usando una herramienta nativa de los propios desarrolladores de Docker, **Docker Swarm**.  

📌 **¿Por qué Swarm para empezar?**  
- Está **integrado en Docker** (no requiere instalar nada adicional).  
- Es más **fácil de aprender** que Kubernetes.  
- Permite hacer un **clúster en minutos** con comandos simples.  

### 📎 Lista de comandos Swarm  
| Comando                                | Descripción |
|----------------------------------------|-------------|
| `docker swarm init`                    | Inicializa un nuevo clúster de Swarm en el nodo actual. |
| `docker swarm join --token <TOKEN> <IP>:2377` | Une un nodo al clúster de Swarm especificado. |
| `docker swarm leave`                    | Hace que un nodo abandone el clúster de Swarm. |
| `docker node ls`                         | Muestra la lista de nodos en el clúster. |
| `docker node inspect <NODO>`             | Muestra detalles de un nodo específico. |
| `docker node promote <NODO>`             | Promueve un nodo a manager en el clúster. |
| `docker node demote <NODO>`              | Degrada un nodo manager a worker. |
| `docker service create --name <SERVICIO> <IMAGEN>` | Crea un nuevo servicio en el clúster. |
| `docker service ls`                      | Muestra los servicios desplegados en el clúster. |
| `docker service ps <SERVICIO>`           | Muestra las tareas de un servicio. |
| `docker service inspect <SERVICIO>`      | Muestra detalles de un servicio. |
| `docker service scale <SERVICIO>=<REPLICAS>` | Escala un servicio al número de réplicas especificado. |
| `docker service update --image <IMAGEN> <SERVICIO>` | Actualiza la imagen de un servicio en ejecución. |
| `docker service rm <SERVICIO>`           | Elimina un servicio del clúster. |
| `docker stack deploy -c <ARCHIVO.YML> <NOMBRE>` | Despliega una pila de servicios definida en un archivo Compose. |
| `docker stack ls`                        | Muestra las pilas desplegadas en el clúster. |
| `docker stack ps <NOMBRE>`               | Muestra los servicios de una pila específica. |
| `docker stack rm <NOMBRE>`               | Elimina una pila del clúster. |  

🔹 **Conceptos clave en Swarm:**  
- **Manager:** Nodo que coordina el clúster.  
- **Worker:** Nodos que ejecutan los contenedores.  
- **Service:** Definición de qué contenedor se ejecuta y cuántas réplicas.

![Docker_Swarm_Schema](/0.0_resources/swarm-diagram.png)  


## 📗 7.1 - Iniciar un clúster Docker Swarm en local  
Con este ejercicio aprenderemos a configurar un clúster **Docker Swarm** en un solo nodo (modo desarrollo).  

**1. Inicializamos Docker Swarm**  
```powershell
docker swarm init
```
✅ Esto convierte tu máquina en el **Manager** del clúster.  

Para comprobar el estado del clúster:  
```powershell
docker info | Select-String "Swarm"
```
🔹 Salida esperada: `Swarm: active`  

> [!Note]
> Una vez iniciamos el servicio Swarm este se queda activado siempre, si deseáramos parar el servicio de Swarm deberemos ejecutar el siguietne comando:  
> **`docker swarm leave --force`**: Esto desactiva Swarm completamente y elimina la configuración del clúster si la hubiese.  

## 📗 7.2 - Crear y escalar un servicio en Swarm  
Con este ejercicio aprenderemos a ejecutar un servicio en el clúster y escalarlo.  

**1. Creamos un servicio replicado**  
```powershell
docker service create --name web --replicas 2 -p 8080:80 nginx
```  
> [!Note]
> `--replicas 2`: Inicia 2 instancias del servicio.  
> `-p 8080:80`: Expone el servicio en el puerto 8080 del host.  

**2. Verificamos el estado del servicio**  
```powershell
docker service ls
```  
✅ Debería mostrar **el servicio `web` con 2 réplicas en ejecución**.  

**3. Escalamos el servicio a 4 instancias**  
```powershell
docker service scale web=4
```
🔹 Verificamos nuevamente:  
```powershell
docker service ps web
```
✅ Ahora debería haber **4 instancias activas** del servicio `web`.  

## 📗 7.3 - Probar el balanceo de carga  
Con este ejercicio comprobaremos como Swarm distribuye las peticiones entre los contenedores.  

**1. Hacemos varias peticiones a la aplicación**  
```powershell
for ($i=0; $i -lt 10; $i++) { Invoke-WebRequest -Uri http://localhost:8080 -UseBasicParsing | Select-String "Welcome" }
```
✅ Cada petición será atendida por una instancia diferente del servicio.  

**2. Reducimos el número de instancias**  
```powershell
docker service scale web=2
```  
🔹 Swarm eliminará automáticamente los contenedores sobrantes.  

**3. Eliminamos el servicio**  
```powershell
docker service rm web
```  

## 📗 7.4 - Añadir nodos adicionales al clúster Swarm (ejercicio grupal)  
Hasta el momento hemos visto como funcionaba un sistema distribuido pero faltaría por ver como sería un proceso de clusterización. Con este ejercicio vamos a agregar más nodos al clúster y distribuir los contenedores en múltiples máquinas virtuales.  

Antes de empezar tenemos que lanzar los siguientes comandos por consola para habilitar reglas en el firewall permitiendo que permitan tráfico swarm entre todos los nodos:
```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4" -Direction Inbound -Action Allow -Protocol ICMPv4
```
```powershell
New-NetFirewallRule -DisplayName "Docker Swarm TCP" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 2377,7946
```
```powershell
New-NetFirewallRule -DisplayName "Docker Swarm UDP" -Direction Inbound -Action Allow -Protocol UDP -LocalPort 7946,4789
```  

**1. Un participante actuará como nodo `manager` y obtendrá el comando para añadir otros nodos `worker` al clúster.**  
Desde el nodo **manager**, ejecutaremos:  
```powershell
docker swarm join-token worker
```
✅ Esto devolverá un comando parecido a:  
`docker swarm join --token SWMTKN-1-xxx <IP_DEL_MANAGER>:2377`  

**2. Agregamos nuevos nodos al clúster.**  
El participante que esté actuando como **manager** deberá compartir el código generado en el paso anterior con el resto de participantes.  
En cada **VM adicional** que actuará como **worker**, ejecutaremos el comando compartido por el **manager**.  

**3. Verificamos que los nodos están conectados.**  
Desde el nodo **manager**, ejecutaremos:
```powershell
docker node ls
```

✅ Ahora deberíamos ver varios nodos (manager y workers) en el clúster.  

> [!Note]
> Si queremos sacar un nodo **worker** del clúster bastaría con ejecutar el siguiente comando desde el mismo:  
> `docker swarm leave`  

**4. Redistribuiremos la aplicación en múltiples nodos.**  
Desde el nodo **manager**, lanzaremos el siguiente comando:  
```powershell
docker service scale web=6
```
💡 Swarm balanceará automáticamente las nuevas instancias entre los nodos disponibles. Cada nodo **worker** podrá ver desde **docker desktop** o la `consola`, como se están desplegando contenedores docker nuevos desde su VM.  

## 📗 7.5 - Desplegar servicio de monitorización en Swarm montado con Docker Compose  
Para finalizar el módulo y el curso, vamos a poner en práctica todo lo visto durante este y vamos a montar un servicio de monitorización en nuestro clúster Swarm usando una plantilla de Docker Compose. En ella, configuraremos las herramientas como cAdvisor, Prometheus y Grafana para llevar a cabo la monitorización del clúster.  
Para este ejercicio usaremos los recursos de la carpeta 📂 **`./7.5_swarm_monitoring/`** en nuestro repositorio.

**1. Revisamos el archivo `docker-compose.yml` e identificamos todos los elementos.**
- ¿Hay volúmenes montados?
- ¿Se va a crear alguna red?
- ¿Cuantos servicios identificas?

**2. Desplegamos la monitorización en Swarm.**  
Para poder desplegar la monitorización, necesitaremos antes realizar ciertos ajustes en Docker Desktop para que publique sus métricas. Para ello abriremos nuestra app de Docker Desktop e iremos a **`Settings --> Docker Engine`**.  
Aquí deberemos cambiar algún ajuste, en particular el de `experimental` y, además, añadir el parámetro de **`metrics-addr`**. Debería quedar tal que así:  
```json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": true,
  "metrics-addr": "127.0.0.1:9323"
}
```
Ahora pulsamos el botón de **`Apply & restart`** y volvemos a nuestra consola.  
Ejecutamos:  
```powershell
docker stack deploy -c docker-compose.yml monitoring
```
✅ Si todo ha ido bien, veremos como poco a poco comienza a desplegarse toda nuestra solución de monitorización y podremos acceder a las siguientes direcciones desde nuestro navegador:  
- **cAdvisor:** `http://localhost:8081`  
- **Prometheus:** `http://localhost:9090`  
- **Grafana:** `http://localhost:3000`  

Para poder visualizar toda la info en Grafana seguiremos los siguientes pasos:
1. Una vez dentro del panel de control de Grafana `http://localhost:3000` nos iremos a **`Connections > Data Sources`**.  
2. Click en **`Add data source`**.  
3. Seleccionamos **`Prometheus`**.  
4. En **Prometheus server URL** ponemos: `http://prometheus:9090` y le damos a **Save & Test** (abajo del todo).  
5. Ahora nos vamos a **`Dashboards > New > Import`**
6. Usamos el ID **`1860`** + **Load**
7. En la nueva pantalla, abajo en **Prometheus** seleccionamos **`prometheus`** y pulsamos **Import**.

# 🧽 Es la hora de la limpieza...
Una vez finalizado todo, si queremos dejar nuestro equipo limpio lanzaremos los siguientes comandos:  
- **`docker swarm leave --force`**: paramos el servicio de Swarm.  
- **`docker stop $(docker ps -q)`**: con este comando pararemos todos los contenedores activos que no pertenecieran al servicio de Swarm.    
- **`docker rm $(docker ps -aq)`**: una vez parados los contenedores, los borraremos con este comando.  
- **`docker prune system -a`**: este comando hará una limpieza de todos los servicios residuales que queden en nuestro sistema (volúmenes, redes, imágenes, etc..).

# 🫰 Y para finalizar ..  
¡Gracias a todos por vuestro tiempo y dedicación durante este curso de Docker! Esperamos que hayáis encontrado útil todo lo aprendido y que os ayude en vuestros futuros proyectos. Si en algún momento os surge alguna duda o necesitáis aclaraciones, no dudéis en contactarnos.  
Ha sido todo un placer compartir este aprendizaje con vosotros.  
¡Nuevamente gracias y a seguir "dockerizando"! 🐳
  
