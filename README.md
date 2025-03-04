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

## 1. Inspeccionar imagen ngninx
Comenzaremos realizando una búsqueda de la imagen que vamos a usar durante el curso. Se trata de una imagen [nginx](https://hub.docker.com/_/nginx). Dentro de la referencia de esta imagen, podemos ver la descripción de la imagen, como usarla, etc., y en la parte de tags podemos ver las distintas versiones que hay para esta imagen. Como nosotros queremos que sea una imagen liviana, nos centraremos en usar la imagen de nginx basada en alpine.

## 2. Identificarnos en el registro
Para poder trabajar durante el curso con nuestro repositorio de Docker HUB, lo primero que haremos será identificarnos en el repositorio. Como las credenciales se guardan en cache, quedaremos logeados para el resto de sesiones.

### Instrucciones
- Registrate con tu usuario y contraseña de Docker Hub
`docker login docker.io -u cursodockerfuturs`
- Accede usando la contraseña proporcionada

## 3. Levantar el contenedor
Primero levantaremos el contenedor como veníamos haciendo hasta ahora:
- `docker run -d --name web-ngninx -p 81:80 nginx:alpine`

  > [!RECORDATORIO]
  > - `-d` para correr en segundo plano
  > - Importante darle nombre al contenedor
  > - No puedo levantar contenedores que usen el mismo nombre que otro, aunque esté muerto
  > - No puedo levantar contenedores que usen el mismo puerto que otro que esté levantado

Accedemos a nuestra URL http://localhost:81 para comprobar nuestra aplicación web.

Ahora vamos a levantar de nuevo el contenedor, pero asignandole una variable de entorno:
`docker run -d --name web-ngninx -e PRUEBA=1234 -p 81:80 nginx:alpine`

Accedemos de nuevo a nuestra URL http://localhost:82 para comprobar nuestra aplicación web.

Para poder consultar las variables de entorno que tenemos en nuestro contenedor, tenemos varias opciones:

*Opción 1*
Entramos al contenedor que hemos creado y mostramos las variables:
- `docker exec -it web-ngninx2 sh`
- `echo $PRUEBA`
- `printenv`

*Opción 2*
Inspeccionamos el contenedor docker que hemos creado con el comando `docker inspect web-ngninx2`.
Con esta opción además podremos ver mucha más información útil como el ID, argumentos que tiene, el estado del contenedor, la imagen, el puerto, los volumenes, variables de entorno, networking, etc...

Una vez que veamos que está correcta podemos eliminar los contenedores:
- `docker rm -f web-nginx web-ngninx2`

  > [!RECORDATORIO]
  > - `-f` para forzar. De esta forma evito tener que parar previamente el contenedor.