# Le Docker Hub :
## 1 Commencer avec Hello world:

### démarrage d'un conteneur

```console
docker run hello-world
```
### 

**S'il n'est pas disponible en local il est récupéré sur la registry Docker officielle.**

**Résultat :**
```console
->docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:13e367d31ae85359f42d637adf6da428f76d75dc9afeb3c21faea0d976f5c651
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```

> le conteneur a démarré, puis affiché du contenu, et il a fini par s'arrêter. Si vous souhaitez que votre conteneur reste allumé jusqu’à l'arrêt du service qu'il contient, vous devez ajouter l’argument **--detach (-d)** . Celui-ci permet de ne pas rester attaché au conteneur, et donc de pouvoir lancer plusieurs conteneurs.

# Démarrez un serveur Nginx avec un conteneur Docker
lancer un conteneur qui démarre un serveur Nginx (serveur web) en utilisant deux options :
``` 
docker run -d -p 8080:80 nginx 
```

> **-d** pour détacher le conteneur du processus principal de la console. Il vous permet de continuer à utiliser la console pendant que votre conteneur tourne sur un autre processus ;

> **-p** pour définir l'utilisation de ports. Dans votre cas, vous lui avez demandé de transférer le trafic du port 8080 vers le port 80 du conteneur. Ouvrez la page par défaut depuis la pop-up s'affichant en bas à droite de la fenêtre (voir ci-après) soit dans le preview de votre IDE soit dans un nouvel onglet

> ![alt](./2022-06-29_23h41_58.png)

"rentrer" dans votre conteneur Docker pour pouvoir y effectuer des actions

```
docker exec -ti ID_RETOURNÉ_LORS_DU_DOCKER_RUN bash
```
> ID_RETOURNÉ_LORS_DU_DOCKER_RUN est l'id affiché en bas du résulat de la command ```docker run``` précédente (exemple : 5fff095cb866df2f1451aaaea88166560200a7b26e3be2e6749887ba41a80e5e)

rentrer dans le folder contenant le fichier ```index.html``` pour modifier son contenu et voir le résultat en direct sur la page affichée précédemment

```
cd /usr/share/nginx/html
```
# Arrêtez votre conteneur Docker
```
docker stop ID_RETOURNÉ_LORS_DU_DOCKER_RUN
```

Il est donc possible de le supprimer avec la commande :

```
docker rm ID_RETOURNÉ_LORS_DU_DOCKER_RUN
```
# Récupérez une image du Docker Hub
téléchargez une image directement depuis le Docker Hub, et vous la stockez en local sur le workspace
```
docker pull hello-world
```
```
** résulta t**
Using default tag: latest
latest: Pulling from library/hello-world
Digest: sha256:13e367d31ae85359f42d637adf6da428f76d75dc9afeb3c21faea0d976f5c651
Status: Image is up to date for hello-world:latest
docker.io/library/hello-world:latest
```
# Affichez l'ensemble des conteneurs existants

vous pouvez avoir besoin de savoir si les conteneurs sont toujours actifs ; pour cela, vous devez utiliser la commande
```
docker ps
```
```
** résultat **
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                   NAMES
efeed37e9d0e   nginx     "/docker-entrypoint.…"   47 seconds ago   Up 46 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   eager_haslett
```
voir l'ensemble des images présentes

```
docker images -a
```
```
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
nginx         latest    55f4b40fe486   6 days ago     142MB
hello-world   latest    feb5d9fea6a5   9 months ago   13.3kB
```

# Comment nettoyer mon système
```
docker system prune
```
```
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N] y
Deleted Containers:
e2b00ee8e860fc4c0c4d42af50e01a0a46dd8f19d1d00496b23502394840a348

Total reclaimed space: 0B
```

Celle-ci va supprimer les données suivantes :

- l'ensemble des conteneurs Docker qui ne sont pas en status running ;
- l'ensemble des réseaux créés par Docker qui ne sont pas utilisés par au moins un conteneur ;
- l'ensemble des images Docker non utilisées ;

l'ensemble des caches utilisés pour la création d'images Docker.

# Créez votre premier Dockerfile

Vous savez maintenant utiliser l'interface de commande de Docker et récupérer des images depuis le Docker Hub. Mais comment créer votre propre image ?

Vous allez créer  une image Docker, dans laquelle installer Node.js, ainsi que les différentes dépendances de votre projet.

Pour cela, créez un fichier nommé "Dockerfile". Dans ce fichier Dockerfile, vous allez trouver l'ensemble de la recette décrivant l'image Docker dont vous avez besoin pour votre projet.

> Pour illustrer la création de Dockerfile, vous allez utiliser le code de l'application présente sur le repository suivant : https://github.com/OpenClassrooms-Student-Center/ghost-cms. L'application possède déjà un Dockerfile fonctionnel, vous allez le recréer. Pensez bien à vous mettre dans le répertoire racine du projet !

# Créez les instructions dans votre Dockerfile
créez un fichier nommé "Dockerfile", puis de définir dans celui-ci l'image que vous allez utiliser comme base, grâce à l'instruction ``FROM``  . Vous allez utiliser une image de base **Debian 9**.

Ajouter la ligne :
```
FROM debian:9
```
L'instruction ``FROM`` n'est utilisable qu'une seule fois dans un Dockerfile.

Ensuite, utilisez l'instruction ``RUN`` pour exécuter une commande dans votre conteneur.

Ajoutez :
```
RUN apt-get update -yq \
&& apt-get install curl gnupg -yq \
&& curl -sL https://deb.nodesource.com/setup_10.x | bash \
&& apt-get install nodejs -yq \
&& apt-get clean -y
```
Puis, utilisez l'instruction  ``ADD``  afin de copier ou de télécharger des fichiers dans l'image. Dans votre cas, vous l'utilisez pour ajouter les sources de votre application locale dans le dossier ``/app/`` de l'image.

Pour cela ajoutez :
```
ADD . /app/
```
Utilisez ensuite l'instruction ``WORKDIR`` qui permet de modifier le répertoire courant. La commande est équivalente à une commande ``cd`` en ligne de commande. L'ensemble des commandes qui suivront seront toutes exécutées depuis le répertoire défini.
```
WORKDIR /app
```
Puis, l'instruction ``RUN`` suivante permet d'installer le package du projet Node.js.
```
RUN npm install
```
Maintenant que le code source et les dépendances sont bien présents dans votre conteneur, vous devez indiquer à votre image quelques dernières informations.
```
EXPOSE 2368
VOLUME /app/logs
```
L'instruction ``EXPOSE`` permet d'indiquer le port sur lequel votre application écoute. L'instruction ``VOLUME`` permet d'indiquer quel répertoire vous voulez partager avec votre host.

> Les instructions ``EXPOSE`` et ``VOLUME`` ne sont pas nécessaires au bon fonctionnement de votre image Docker. Cependant, les ajouter permet une meilleure compréhension pour l’utilisateur des ports d'écoute attendus, ainsi que des volumes partageables.

L'instruction ``CMD``  doit toujours être présente, et la placer en dernière ligne pour plus de compréhension : Elle permet à votre conteneur de savoir quelle commande il doit exécuter lors de son démarrage.
```
CMD npm run start
```
## **En résumé, voici votre Dockerfile une fois terminé :**##
```
FROM debian:9

RUN apt-get update -yq \
&& apt-get install curl gnupg -yq \
&& curl -sL https://deb.nodesource.com/setup_10.x | bash \
&& apt-get install nodejs -yq \
&& apt-get clean -y

ADD . /app/
WORKDIR /app
RUN npm install

EXPOSE 2368
VOLUME /app/logs

CMD npm run start
```

# Créez votre fichier .dockerignore
Votre Dockerfile est maintenant prêt à fonctionner ! Cependant, il vous reste encore quelques petites modifications à faire.

Sur un projet Git, vous utilisez un fichier ``.gitignore`` ; sur Docker il existe le même type de fichier. Celui-ci permet de ne pas copier certains fichiers et/ou dossiers dans votre conteneur lors de l’exécution de l'instruction ``ADD``  .

À la racine de votre projet (soit à côté de votre fichier **Dockerfile**), vous devez créer un fichier ``.dockerignore`` qui contiendra les lignes suivantes :

```
node_modules
.git
```

# Lancez votre conteneur personnalisé !
Vous pouvez maintenant créer votre première image Docker !
```
docker build -t monPremier-docker-build .
```
L'argument **-t** permet de **donner un nom à votre image** Docker. Cela permet de retrouver plus facilement votre image par la suite.

Le ``.`` est le répertoire où se trouve le Dockerfile ; dans votre cas, à la racine de votre projet.

Maintenant, vous pouvez lancer votre conteneur avec la commande ``docker run`` :
```
docker run -d -p 2368:2368 monPremier-docker-build
```
Vous retrouvez, dans le dossier ``logs``  , les logs de votre application, et vous pourrez y accéder sur le port 2368  , soit via la pop-up s'affichant en bas à droite.

# En résumé
Pour créer une image Docker, vous savez utiliser les instructions suivantes :

FROM qui vous permet de définir l'image source ;

RUN qui vous permet d’exécuter des commandes dans votre conteneur ;

ADD qui vous permet d'ajouter des fichiers dans votre conteneur ;

WORKDIR qui vous permet de définir votre répertoire de travail ;

EXPOSE qui permet de définir les ports d'écoute par défaut ;

VOLUME qui permet de définir les volumes utilisables ;

CMD qui permet de définir la commande par défaut lors de l’exécution de vos conteneurs Docker.
# Créez votre image sur le Docker Hub
Pour partager l'image que venez de créer, vous allez la publier Docker Hub.

Avant tout créer votre compte sur https://hub.docker.com/ puis connectez vous avec ce compte.

Puis, cliquez sur le lien Create Repository. Vous arrivez alors sur une page où vous devez saisir le nom de votre image, ainsi qu'une description.

Appelez cette image monPremier-docker-build  . Vous pouvez aussi appeler votre repository de la même façon sur le Docker Hub.

se connecter depuis le terminal de votre workspace :
```
docker login -u YOUR_USERNAME
```
> rentrez votre mot de passe 

puis suivez les instructions suivantes :
```
docker tag monPremier-docker-build:latest YOUR_USERNAME/monPremier-docker-build:latest
```
>  Celle-ci va créer un lien entre votre image monPremier-docker-build:latest créée précédemment et l'image que vous voulez envoyer sur le Docker Hub YOUR_USERNAME/monPremier-docker-build:latest .

Vous pouvez maintenant exécuter la dernière commande nécessaire pour envoyer votre image vers le Docker Hub. Pour cela, vous allez exécuter la commande

```
docker push YOUR_USERNAME/monPremier-docker-build:latest
```
Vérifier le résultat sur la page de votre image Docker Hub vous pourrez voir qu'il existe une première version de celle-ci.
Vous pouvez créer d'autres versions de votre image en remplaçant le :latest par une autre chaîne de caractères. Cependant, il faut bien faire attention, car l'image utilisée par défaut sera toujours l'image :latest .

# Trouvez l'image qui vous correspond
Vous pouvez avoir besoin de rechercher des images dans le Docker Hub, mais il existe deux types d'images différents :

- les images officielles ;

- les images personnelles.

Si vous recherchez une image, vous avez deux façons de procéder :

la solution  en ligne de commande ;

la solution classique avec l'interface web.

En ligne de commande : 
```
docker search nginx
```

```
NAME                                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                                             Official build of Nginx.                        17016     [OK]       
linuxserver/nginx                                 An Nginx container, brought to you by LinuxS…   169                  
bitnami/nginx                                     Bitnami nginx Docker Image                      133                  [OK]
ubuntu/nginx                                      Nginx, a high-performance reverse proxy & we…   52                   
bitnami/nginx-ingress-controller                  Bitnami Docker Image for NGINX Ingress Contr…   18                   [OK]
rancher/nginx-ingress-controller                                                                  10                   
ibmcom/nginx-ingress-controller                   Docker Image for IBM Cloud Private-CE (Commu…   4                    
bitnami/nginx-ldap-auth-daemon                                                                    3                    
vmware/nginx                                                                                      2                    
circleci/nginx                                    This image is for internal use                  2                    
rancher/nginx                                                                                     2                    
bitnami/nginx-exporter                                                                            2                    
rancher/nginx-ingress-controller-defaultbackend                                                   2                    
bitnami/nginx-intel                                                                               1                    
vmware/nginx-photon                                                                               1                    
kasmweb/nginx                                     An Nginx image based off nginx:alpine and in…   1                    
rapidfort/nginx                                   RapidFort optimized, hardened image for NGINX   1                    
wallarm/nginx-ingress-controller                  Kubernetes Ingress Controller with Wallarm e…   1                    
rancher/nginx-ssl                                                                                 0                    
rancher/nginx-conf                                                                                0                    
continuumio/nginx-ingress-ws                                                                      0                    
rancher/nginx-ingress-controller-amd64                                                            0                    
ibmcom/nginx-ppc64le                              Docker image for nginx-ppc64le                  0                    
ibmcom/nginx-ingress-controller-ppc64le           Docker Image for IBM Cloud Private-CE (Commu…   0                    
rancher/nginx-proxy                                                                               0 
```