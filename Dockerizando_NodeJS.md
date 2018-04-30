# Dockerizando una aplicacion Node.js + Express.js

## Requisitos
* Experiencia previa con Docker
* Experiencia previa con Node.js
* Experiencia previa con linea de comandos.

## Objetivo
El objetivo de este post es mostrarte como dockerizar una aplicacion Node.js + Express.js de la manera mas optima posible para un ambiente de desarrollo. Aprenderas sobre como aprovechar y utilizar correctamente las capas de docker.

## Teoria necesaria
Vamos a repasar solo la teoria necesaria para completar satisfactoriamente este proceso. Si necesitas mas informacion puedes ver el siguente post donde exploramos varios de los aspectos importantes de Docker.

### Dockerfiles
Existen diferentes maneras de crear imagenes en Docker, una de las formas mas comunes de crear imagenes es utilizando archivos Dockerfile, los cuales contienen las instrucciones para la construccion de la imagen. 

```
FROM <imagen_base>

MAINTAINER <tu_nombre>

RUN <comando>
RUN <otro_comando>

...
```

### Capas
Cada vez que se ejecuta una instruccion del archivo Dockerfile al momento de construccion de una imagen, docker crea una capa nueva. Si nuestro archivo Dockerfile contiene 5 instrucciones, este proceso de build generara 5 capas nuevas, las cuales son conocidas como imagenes intermedias.

### Comando Docker build
Este comando nos permite crear una nueva imagen utilizando un archivo Dockerfile. Indicamos el nombre de nuestra nueva imagen utilizando el flag -t e indicamos la ubicacion de nuestro contexto como primer parametro:
```
$ docker build -t <nombre_imagen> <contetext>
```
### Comando Docker run
Este comando lo utilizaremos para iniciar nuestro container despues de haberlo construido. Le indicaremos a docker que queremos exponer el puerto del container utilizando el flag -p en donde indicamos como primer valor el puerto a utilizar en nuestro host e indicaremos el puerto dentro del container como segundo valor. Como paremtro final indicamos el nombre de la imagen que deseamos utilizar para iniciar este nuevo contenedor.

```
$ docker run -p <host_port>:<container_port> <nombre_imagen>
```

## Manos a la obra
Empezaremos creando un servidor sencillo eny procederemos acontinuacion a dockerizarlo.

### Creando el proyecto:
crea un directorio nuevo en el lugar que deseas y luego navega dentro de el:
```
$ mkdir digitalpark_nodeapp
$ cd digitalpark_nodeapp

```
crearemos un proyecto nuevo utilizando el comando npm init y aceptaremos los valores por defecto presionando la tecla enter:
```
$ npm init

name: (digitalpark_nodeapp)
version: (1.0.0)
description:
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
About to write to /digitalpark_nodeapp:

{
  "name": "digitalpark_nodeapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}


Is this ok? (yes)
```
Al finalizar la inicializacion del modulo se creara un archivo package.json con la metadata de nuestro modulo. Ya tenemos el esqueleto de nuestro proyecto. A continuacion procederemos a implementar el codigo.

### Programando nuestra aplicacion

Para crear un servidor HTTP en Node.js necesitamos instalar una libreria llamada Express.js. Express es el framework mas popular para crear servidores en Node.js. Puedes encontrar mas informacion en visitando su sitio oficial en el siguiente link: https://node.org.

Ejecuta el siguiente comando para proceder con la instalacion:

```
$ npm install --save express
```

Una vez termine el proceso de instalacion, crea el archivo server.js con el siguiente contenido:
```
const express = require('express');
const http = require('http');
const app = express();

app.use('/', (req, res) => {
  res.send('Hola Mundo!')
});

http.createServer(app).listen(3000, () => {
  console.log('Servidor funcionando en el puerto 3000')
});

```

Antes de probar nuestro servidor, modifiquemos el archivo package.json para indicar que nuesrto archivo principal se llama server.js, y configurar un script para iniciar nuestro archivo server.js. Sobre el archivo package.json aplica los siguientes cambios:

```
{
  "name": "digitalpark_nodeapp",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.16.3"
  }
}
```

Listo! procederemos a ejecutar nuestro servidor con el comando npm start:

```
$ npm start

> node server.js
Servidor funcionando en el puerto 3000
```

Como vemos en la consola, nuestro servidor esta corriendo sobre el puerto 3000. En tu navegador web dirigete a http://localhost:3000 para ver el mensaje "Hola Mundo!". Puedes utilizar el comando curl si te gusta trabajar con la consola:

```
$ curl http://localhost:3000

Hola Mundo!
```

Listo!, ya tenemos nuestro servidor HTTP basico. Es hora de dockerizarlo. En este punto deberiamos de tener una carpeta llamada digitalpark_nodeapp con los siguientes elementos:

* package.json
* server.js
* node_modules

Si es asi. En hora buena! podemos continuar.

### Dockerizando nuestro servidor

Como hemos visto en la parte teorica, necesitaremos de un archivo Dockerfile para construir nuestra imagen. Crea un archivo Dockerfile en la raiz de la carpeta digitalpark_nodeapp y agrega el siguiente contenido:

```
FROM node

WORKDIR /app

COPY package.json /app
RUN npm install

COPY . /app

EXPOSE 3000
CMD ["node", "server.js"]

```

La clave para crear una imagen docker de manera optima reside en copiar el archivo package.json primero antes de copiar todo el proyecto. Esto sucede debido a que las imagenes toman un tiempo considerable al momento de contruir. Docker reconstruira toda la imagen cada vez que detecte que cierto elemento que compone una capa ha cambiado. Por ejemplo, si copiamos todo el contenido de nuestro proyecto con un solo comando COPY este generara una sola capa, de modo que cada vez que se modifique el codigo en cualquier archivo .js, Docker reconstruira todo.

<AGREGAR IMAGENES EXPLICATIVAS>
<VIDEO DE REFERENCIA>

Ahora constryamos nuestra imagen. Para esto ejecuta el siguiente comando:

```
$ docker build -t ServidorHolaMundo .
```

Sabemos por teoria que nuestra imagen ser llamara ServidorHolaMundo y que el archivo Dockerfile se encuentra en la raiz del proyecto. Esto lo indicamos con un punto (.).

Una vez tenemos la imagen procederemos a crear un contenedor y verlo en ejecucion. Ejecuta el siguiente comando para crear un nuevo contenedor y ver nuestro ServidorHolaMundo en funcionamiento:

```
$ docker run --rm -p 3000:3000 ServidorHolaMundo
```

<Si utilizas windows o linuxx> <Video>














