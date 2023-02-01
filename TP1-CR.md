# TP1 - DOCKER

<div style="font-size:13px; font-weight:700; color:#C4C4C4; margin-bottom:30px">Louisa Chaduc - 30.01.2023</div>

## <span style="color:#D86D6F"> 0 - Initialisation </span>

Commandes réalisées pour ajouter les images préconfigurées utiles à notre application, avec les versions appropriées :

```bash
docker pull httpd:2.4             # HTTP server
docker pull openjdk:17            # Backend API
docker pull postgres:14.1-alpine  # Database
```

De plus, je crée les trois dossiers nécessaires à chaque image de mon application 3-tier :

```
s8-devops
├─ api
├─ http
└─ database
```

## <span style="color:#D86D6F"> 1 - Database </span>

<div style="border: 1px solid #AB1B1B; padding: 8px; background: #FBEFF2; font-weight:300; margin-bottom:20px">
<span style="font-weight:400">Question 1-1 :</span>
Documenter les commandes et le Dockerfile nécessaires à notre container Database commands and Dockerfile.
</div>

La première étape est de créer le fichier de configuration <span style="color:#2D9469">`Dockerfile`</span> dans le dossier de la Database et l'initialiser.


```yaml
FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
POSTGRES_USER=usr

COPY CreateScheme.sql /docker-entrypoint-initdb.d
COPY InsertData.sql /docker-entrypoint-initdb.d
```

Après cette configuration, voici les étapes à réaliser pour monter notre environnement 
- Build de l'image custo de la database :
  docker build -t youreastonefox/database .

- Création du network :  
  docker network create app-network

- Import de l'image de l'adminer :
  git pull adminer

- Run database :
  docker run --name=database --net=app-network -e POSTGRES_PASSWORD=pwd -d -v dbvolume:/var/lib/postgresql/data youreastonefox/database

- Run adminer : (hote:container)
  docker run -p "8090:8080" --name=adminer --net=app-network -d adminer

Adminer est le client SQL qui permet d'accéder à la database.

- Identification à la BD
  Postgresql
  database
  usr
  pwd
  db

- Volume : docker volume create dbvolume
  Décrire le comportement d'un volume
  On ne sait pas bien où c'est stocké mais on voit que ça fonctionne
  Why do we need a volume to be attached to our postgres container?

## API

### Basics

- Import de l'image de l'openjdk :
  docker pull openjdk:17

- Build de l'image custo de l'api :
  docker build -t youreastonefox/api .

- Run api :
  docker run --name=api --net=app-network -d youreastonefox/api

```
FROM openjdk:17

COPY Main.class .

CMD ["java", "Main"]
```

TODO: Screen du hello world

### Multistage build

```
# FROM openjdk:11
# Build Main.java
# TODO : in next steps (not now)

# FROM openjdk:11-jre
# Copy resource from previous stage
# COPY --from=0 /usr/src/Main.class .
# Run java code with the JRE
# TODO : in next steps (not now)


# FROM openjdk:17

# COPY Main.class .

# CMD ["java", "Main"]

# Build
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
ENV MYAPP_HOME /
WORKDIR $MYAPP_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Run
FROM amazoncorretto:17
ENV MYAPP_HOME /
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

ENTRYPOINT java -jar myapp.jar
```

- Import de maven :
  docker pull maven:3.8.6-amazoncorretto-17

- Build de l'image custo de l'api :
  docker build -t youreastonefox/api .

- Run api :
  docker run -p "8080:8080" --name=api -d youreastonefox/api

docker run -p "8080:8080" --net=app-network --name=api -d youreastonefox/api

TODO: Commenter les Dockerfile

## HTTP Server

```
FROM httpd:2.4

COPY . /usr/local/apache2/htdocs/
```

docker stats
docker inspect
docker logs
docker exec

/usr/local/apache2/conf/httpd.conf

docker build -t youreastonefox/httpdserver .
docker run --net=app-network --name httpdserver -p 80:80 -d youreastonefox/httpdserver

docker exec e30 ls : retourne
bin
build
cgi-bin
conf
error
htdocs
icons
include
logs
modules

docker exec e30 cat /usr/local/apache2/conf/httpd.conf : retourne le contenu de ce fichier de configuration

TODO: Why do we need a reverse proxy?
TODO: Commenter ce qu'on a fait dans le fichier text.txt, copié dans le dockerfile dans la conf approprié, pour que ce soit chargé avec l'image.

- Ajout de :  
   <VirtualHost \*:80>
  ProxyPreserveHost On
  ProxyPass / http://api:8080/
  ProxyPassReverse / http://api:8080/
  </VirtualHost>
- Décommenter la ligne 142 et 145

## Docker Compose

1-3 Document docker-compose most important commands.  
1-4 Document your docker-compose file.

Seule commande utilisée : docker composer up
TODO: Commenter le fichier docker-composer
TODO: Trouver d'autres commandes à utiliser et les expliquer
TODO: Screen de docker et du rendu dans le navigateur

## Docker Hub

docker login

docker tag my-database USERNAME/my-database:1.0

docker tag tp1-backend youreastonefox/tp1-backend:1-0
docker tag tp1-httpd youreastonefox/tp1-httpd:1-0
docker tag tp1-database youreastonefox/tp1-database:1-0

docker push youreastonefox/tp1-backend:1-0
docker push youreastonefox/tp1-httpd:1-0
docker push youreastonefox/tp1-database:1-0

TODO: Ajouter les screens

https://hub.docker.com/repositories/youreastonefox
