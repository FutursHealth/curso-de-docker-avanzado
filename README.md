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

# 📗 1 - Ejercicio Variables de entorno
En este ejercicio práctico aprenderás a usar las variables de entorno en Docker. Veremos además, como consultar las variables que tiene nuestro contenedor, tanto las que nosotros le indiquemos como las que vengan de base en la imagen.

**1. Inspeccionar imagen ngninx**  
Comenzaremos realizando una búsqueda de la imagen que vamos a usar durante el curso. Se trata de una imagen [nginx](https://hub.docker.com/_/nginx). Dentro de la referencia de esta imagen, podemos ver la descripción de la imagen, como usarla, etc., y en la parte de tags podemos ver las distintas versiones que hay para esta imagen. Como nosotros queremos que sea una imagen liviana, nos centraremos en usar la imagen de nginx basada en alpine.

**2. Identificarnos en el registro**  
Para poder trabajar durante el curso con nuestro repositorio de Docker HUB, lo primero que haremos será identificarnos en el repositorio. Como las credenciales se guardan en cache, quedaremos logeados para el resto de sesiones.

- Regístrate con tu usuario y contraseña de Docker Hub  
`docker login docker.io -u cursodockerfuturs`
- Accede usando la contraseña proporcionada

**3. Levantar el contenedor**  
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

# 📗 2 - Ejercicio Volúmenes Docker  
En este ejercicio práctico aprenderás a usar los volúmenes en Docker. Veremos las dos opciones disponibles: Docker Volume y Bind Mount.
Al igual que en el ejercicio anterior, si no conocemos el contenedor que vamos a ejecutar, vamos a la documentación e identificamos los volúmenes que usa.
También aprenderemos a copiar archivos de mi local al contenedor y viceversa.

**1. Inspeccionar imagen ngninx**  
Inspecciona la imagen [nginx](https://hub.docker.com/_/nginx) para determinar cuál es la ruta donde guarda los datos estáticos. Esa ruta será la que nosotros usemos como volumen.

**2. Levantar contenedor docker**  
Ahora levantaremos un contenedor docker como hemos hecho en el ejercicio anterior:  
- `docker run -d --name web-nginx -p 81:80 nginx:alpine`

Comprobamos que se levanta la aplicación correctamente en http://localhost:81

**3. Editar aplicación**  
Ahora entraremos dentro del contenedor que hemos creado para personalizar los datos de mi aplicación.

- `docker exec -it web-nginx sh`
- `cd /usr/share/ngninx/html`
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
2. `docker volume web ls`
3. Creamos de nuevo el contenedor:  
`docker run -d --name web-nginx -v web:/usr/share/nginx/html -p 81:80 nginx:alpine`
4. Inspeccionamos la imagen para ver la parte **Mounts**: `docker inspect web-nginx`

**6. Creamos un archivo local**  
Ahora vamos a crear un archivo en local **index.html** y lo vamos a copiar dentro de la ruta del contenedor.

1. `echo "<h1>hola desde contenedor</h1>" >index.html`
2. `docker cp index.html web-ngninx:/usr/share/nginx/html`

Comprobamos URL `http://localhost:81` y repetimos paso 4.

**Opcional**  
Puedes hacer la prueba de crear un nuevo contenedor (usando otro nombre y otro puerto) usando el mismo volumen. Podrás comprobar que la aplicación que se levanta tiene directamente la información que hemos añadido previamente.

Si no vamos a usar más el volumen y queremos borrarlo: `docker volume rm web`

**7. Bind Mount**  
Para usar la opción de Bind Mount, vamos a crear una carpeta que se llame **static** en nuestra ruta actual. Dentro de esa ruta copiaremos un nuevo archivo **index.html** donde editaremos la información que aparecerá en la web. Luego crearemos el contenedor mapeando la nueva ruta e inspeccionaremos el tipo de volumen que aparece en el contenedor.  

1. `mkdir static`
2. `echo "<h1>hola desde Bind Mount</h1>" >static/index.html`
3. `docker run -d --name web-nginx -v $PWD/static:/usr/share/nginx/html -p 81:80 nginx:alpine`
4. `docker inspect web-ngninx`

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
`docker compose up`

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

## 📗 4.3 - Voting-app
En este último ejercicio vamos a crear una aplicación de votos. Para ello crearemos un **docker-compose.yml** con varios servicios, volúmenes, redes, etc., que contenga todo lo visto anteriormente.  
    
El diagrama de la aplicación es el siguiente:  
   
![Voting_app](/0.0_resources/voting-app.drawio.png)

Las imágenes que vamos a usar son las siguientes:  
1. postgres
2. redis
3. dpage/pgadmin4
4. cursodockerfuturs/voting-app:worker-1.0.0
5. cursodockerfuturs/voting-app:result-2.0.0
6. cursodockerfuturs/voting-app:vote-2.0.0

**Datos necesarios**  
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
   docker stop test-monitoring && docker rm test-monitoring
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
   docker stop cadvisor stress-container && docker rm cadvisor stress-container
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
docker stop logger-container && docker rm logger-container
```
Como podemos ver, Docker almacena logs por defecto en **JSON**, pero podemos acceder a ellos en tiempo real o guardarlos en archivos para análisis posterior.  

## 📗 6.4 - Centralización de logs con Fluentd   
Con este ejercicio aprenderemos a enviar logs de Docker a Fluentd para análisis centralizado.  

**1. Crearemos un archivo de configuración `fluent.conf`**  
```powershell
New-Item -ItemType Directory -Path $PWD\fluentd
Set-Content -Path $PWD\fluentd\fluent.conf -Value @"
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
docker run -d --name fluentd -p 24224:24224 -v ${PWD}\fluentd:/fluentd/etc fluent/fluentd:v1.16-debian-2
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
docker stop fluentd app-logs && docker rm fluentd app-logs
```
⚠️ En este paso nos hemos encontrado que con Docker Desktop for Windows en muchas ocasiones se colgaba intentando parar el contenedor de `app-logs`, si se diera el paso lo que tenemos que hacer es **reiniciar el engine** y ambos contenedores quedarán parados y listos para ser eliminados.  
![Restart_Engine](/0.0_resources/docker_desktop.png)  







