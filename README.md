# nodejsuem
# Repositorio para nuestro curso de kubernetes
Repositorio para la creacion de nuestra web
Codigo creado por Javier Manzanares
 
Para este tutorial, necesitará lo siguiente:

Un usuario sudo en su servidor o en su entorno local.
Docker.
Node.js y npm.
Una cuenta de Docker Hub.
Una cuenta de Git Hub.

Crearemos un package.json con las dependencias de nuestro proyecto: /package.json
{
  "name": "nodejs-image-uem",
  "version": "1.0.0",
  "description": "nodejs image demo",
  "author": "Javier Manzanares",
  "license": "MIT",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "nodejs",
    "bootstrap",
    "express"
  ],
  "dependencies": {
    "express": "^4.16.4"
  }
}

Instale las depdendencias de su proyecto:

npm install

Ya podemos crear de los archivos de la aplicación

Una vez tenemos la app podemos crear el Dockerfile para dockerizar nuestra app:

vi Dockerfile


FROM node:10
RUN mkdir -p /home/node/app/node_modules && chown -R node:node /home/node/app
WORKDIR /home/node/app
COPY package*.json ./
RUN npm install
COPY . .
COPY --chown=node:node . .
USER node
EXPOSE 8080
CMD [ "npm", "start" ] 

Una vez tenemos nuestro docker fiel tenemos que crear el siguiente directorio:

mkidr -p .github/workflows && cd .github/workflows

Y ahora creamos nuestra pipeline:

vi docker-action

name: web  CI/CD
on:
  push:
    branches:
      - 'main'
jobs:
  build-container:
    name: build container
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v2
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USER }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - name: build and push to Docker Hub
        uses: docker/build-push-action@v3
        with:
          push: true
          tags: javiermanzanares/kubernetes:latest, javiermanzanares/kubernetes:${{ github.sha }}

Con esta Pipeline vamos a crear nuestra imagen con la nuestra app y subirla a nuestro Docker Hub.
Ademas crearemos la imagen para que funcione en cualquier plataform (buildx)

Necesitamos configurar las secrets para que Git Hub pueda conectarse a Docker Hub, para ello tenemos que ir al proyecto --> setting -->  
Setting and varibles --> actions --> New Repository secret 

Tebdremos que pasarle las varibles:

Para el usurio:
	 NAME: "DOCKER_USER" y la SECRET "usuario de Docker Hub"

Para la passwd:
	NAME: "DOCKER_PASSWORD" y la SECRET "passwd de Docker Hub"

Ahora cada vez que hagamos un cambio vamos a crear una imagen que subiremos a nuestro Docker Hub, pero antes tendremos que crear el repository en 
Docker Hub, en mi caso se llama "kubernetes" 









