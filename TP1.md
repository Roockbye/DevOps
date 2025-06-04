## 5.Exécuter un serveur web (apache, nginx, …) dans un conteneur docker

J'ai choisi NGINX comme serveur web pour ce TP.

Vérification des versions :
```
docker --version
docker compose version
```
Affiche les images locales disponibles
```
docker ps -a
```

### a. Récupérer l’image sur le Docker Hub

```
docker pull nginx
```

https://hub.docker.com/_/nginx

Récupération de l’image NGINX

```
rocky@pacman:~/Documents/DevOps$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
61320b01ae5e: Pull complete 
670a101d432b: Pull complete 
405bd2df85b6: Pull complete 
cc80efff8457: Pull complete 
2b9310b2ee4b: Pull complete 
6c4aa022e8e1: Pull complete 
abddc69cb49d: Pull complete 
Digest: sha256:fb39280b7b9eba5727c884a3c7810002e69e8f961cc373b89c92f14961d903a0
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

Si on ne précise pas la version, il prend la dernière (latest).

### b. Vérifier que cette image est présente en local

Vérification de la présence locale :

```
docker images
ou
docker ps -a
```
```
rocky@pacman:~/Documents/DevOps$ docker images
REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
nginx             latest    be69f2940aaf   6 weeks ago    192MB
nwodtuhs/exegol   full      7f9f235e780e   2 months ago   52.3GB
```
(note : exegol est pour un projet perso et à ne pas prendre en compte pour ce cours)

### c. Créer un fichier index.html simple

Création d’un fichier index.html simple
```
rocky@pacman:~/Documents/DevOps$ mkdir ~/nginx
rocky@pacman:~/Documents/DevOps$ cd ~/nginx
rocky@pacman:~/nginx$ echo "<h1>Mon serveur web NGINX</h1>" > index.html
rocky@pacman:~/nginx$ cat index.html
<h1>Mon serveur web NGINX</h1>
```
Il y a différentes manières de procéder pour cette étape, comme c'était un simple test rapide je n'ai fait qu'un echo mais pour la suite du TP j'ai crée le fichier index.html.

### d. Démarrer un conteneur et servir la page html créée précédemment à l’aide d’un volume (option -v de docker run)

```rocky@pacman:~/nginx$ docker run --name nginx -v $(pwd)/index.html:/usr/share/nginx/html/index.html:ro -p 8080:80 -d nginx
e8ba350f40e9fefa63ea6bd9a7fce71811bee7562ec616e083e021d87e172c07
```
[nginx](./captures/nginx.png)

Le serveur est disponible sur http://localhost:8080

### e. Supprimer le conteneur précédent et arriver au même résultat que précédemment à l’aide de la commande docker cp

```
rocky@pacman:~/nginx$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS                  PORTS                                     NAMES
e8ba350f40e9   nginx                  "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes            0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   nginx
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago      Exited (0) 9 days ago                                             exegol-STHACK
rocky@pacman:~/nginx$ docker rm -f e8ba350f40e9
e8ba350f40e9
rocky@pacman:~/nginx$ docker images
REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
nginx             latest    be69f2940aaf   6 weeks ago    192MB
nwodtuhs/exegol   full      7f9f235e780e   2 months ago   52.3GB
rocky@pacman:~/nginx$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED      STATUS                  PORTS     NAMES
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago   Exited (0) 9 days ago             exegol-STHACK
rocky@pacman:~/nginx$ 
```
Pour supprimer, ont peut faire avec le container ID ou son nom, je préfère le faire avec le container ID.

```
rocky@pacman:~/nginx$ docker run --name serveur-web -p 8080:80 -d nginx
438337c3df452468b5ef05376ddb164b79a3e17ae53f42625ab8e5451c5e3260
rocky@pacman:~/nginx$ docker cp index.html serveur-web:/usr/share/nginx/html/index.html
Successfully copied 2.05kB to serveur-web:/usr/share/nginx/html/index.html
```

## 6. Builder une image

### a. A l’aide d’un Dockerfile, créer une image (commande docker build)

Dockerfile
```
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```
```
rocky@pacman:~/Documents/DevOps$ docker build -t mon-nginx .
[+] Building 0.1s (7/7) FINISHED                                                                                                                                  docker:default
 => [internal] load build definition from Dockerfile                                                                                                                        0.0s
 => => transferring dockerfile: 103B                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                                                             0.0s
 => [internal] load .dockerignore                                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                             0.0s
 => [internal] load build context                                                                                                                                           0.0s
 => => transferring context: 182B                                                                                                                                           0.0s
 => [1/2] FROM docker.io/library/nginx:latest                                                                                                                               0.0s
 => [2/2] COPY index.html /usr/share/nginx/html/index.html                                                                                                                  0.0s
 => exporting to image                                                                                                                                                      0.0s
 => => exporting layers                                                                                                                                                     0.0s
 => => writing image sha256:2743929e6f93b0277c4203bb430055eab76622131915a96c5b0bda0d21f62d31                                                                                0.0s
 => => naming to docker.io/library/mon-nginx                                                                                                                                0.0s
rocky@pacman:~/Documents/DevOps$ ls
Dockerfile  index.html  nginx.png  README.md  TP1.md
```

### b. Exécuter cette nouvelle image de manière à servir la page html (commande docker run)

```
rocky@pacman:~/Documents/DevOps$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS                  PORTS                                     NAMES
859d2bd59d79   mon-nginx              "/docker-entrypoint.…"   58 seconds ago   Created                                                           nginx-dockerfile
438337c3df45   nginx                  "/docker-entrypoint.…"   45 minutes ago   Up 45 minutes           0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   serveur-web
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago       Exited (0) 9 days ago                                             exegol-STHACK
rocky@pacman:~/Documents/DevOps$ docker run --name custom-nginx -d -p 8085:80 mon-nginx
d33578721bd0e05a6ff8e0592b3a84e16ccbce7d19fbbca1a82706687714a633
```
[custom-nginx](./captures/custom-nginx.png)

### c. Quelles différences observez-vous entre les procédures 5. et 6. ? Avantages et inconvénients de l’une et de l’autre méthode ? (Mettre en relation ce qui est observé avec ce qui a été présenté pendant le cours)

La procédure 5 consiste à utiliser une image existante (comme nginx) et à injecter la page HTML soit via un volume avec l’option -v, soit avec la commande docker cp. Cette méthode est rapide à mettre en place, idéale pour les tests ou le développement local, car elle permet de modifier les fichiers sans reconstruire l’image à chaque fois. Cependant, elle n’est pas adaptée aux déploiements professionnels ou automatisés, car l’image obtenue ne contient pas réellement les fichiers nécessaires. Cela rend difficile la reproductibilité, le versionnage ou l’intégration dans une chaîne CI/CD.

La procédure 6 repose sur la création d’une image personnalisée avec un Dockerfile. Grâce à l’instruction COPY, le fichier HTML est inclus directement dans l’image lors de la phase de build. Cette méthode garantit que le comportement du conteneur est stable, versionnable et entièrement reproductible sur n’importe quel environnement. Elle s’intègre bien dans les pratiques DevOps, notamment dans les processus d’intégration et de déploiement continus. En revanche, elle est un peu plus lente à utiliser en développement, car chaque modification du HTML impose de recompiler l’image.

### En résumé:
- Différences entre étapes 5 et 6 :

Étape 5 utilise une image officielle et monte un volume à la volée ou copie le fichier après lancement.

Étape 6 permet d’automatiser le tout dans une image Docker prête à l’emploi, ce qui est plus réutilisable et portable.

- Avantages de l’étape 6 :

Réutilisable sur d’autres machines

Déploiement standardisé

- Inconvénients :

Moins flexible si on veut modifier le HTML à chaud sans reconstruire l’image.

#### Il  faut séparer les environnements dynamiques de développement des environnements stables et automatisés de production.

## 7. Utiliser une base de données dans un conteneur docker

### a. Récupérer les images mysql:5.7 et phpmyadmin depuis le Docker Hub

```
rocky@pacman:~/Documents/DevOps$ docker pull mysql:5.7
5.7: Pulling from library/mysql
20e4dcae4c69: Pulling fs layer 
1c56c3d4ce74: Pull complete 
e9f03a1c24ce: Pull complete 
68c3898c2015: Pull complete 
6b95a940e7b6: Pull complete 
90986bb8de6e: Pull complete 
ae71319cb779: Pull complete 
ffc89e9dfd88: Pull complete 
43d05e938198: Pull complete 
064b2d298fba: Pull complete 
df9a4d85569b: Pull complete 
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
rocky@pacman:~/Documents/DevOps$ docker pull phpmyadmin
Using default tag: latest
latest: Pulling from library/phpmyadmin
61320b01ae5e: Already exists 
b4ce612dc732: Pulling fs layer 
093338982d92: Pulling fs layer 
0c2a0ba9eb0c: Pulling fs layer 
442abaed7751: Pulling fs layer 
aa4e51934eef: Pulling fs layer 
924949db942b: Pulling fs layer 
153d2fb08c64: Pulling fs layer 
26f17ce45149: Pulling fs layer 
64a857ca8a6a: Pulling fs layer 
7443480a6bdb: Pull complete 
d4dfcfe68eba: Pull complete 
91e76e37da73: Pull complete 
4f4fb700ef54: Pull complete 
708105cf3285: Pull complete 
97739253e39f: Pull complete 
6722b89322cb: Pull complete 
9ebc473a2663: Pull complete 
39ff72c5f974: Pull complete 
2daa1390d242: Pull complete 
Digest: sha256:73467128842bc4406372310f068bc9ccb6727a82c7b5dc9c4f3d815ead33eab8
Status: Downloaded newer image for phpmyadmin:latest
docker.io/library/phpmyadmin:latest
```
Ici, on précise la version de mysql qu'on souhaite récuperer, sinon il prendra automatiquement la dernière. 

### b. Exécuter deux conteneurs à partir des images et ajouter une table ainsi que quelques enregistrements dans la base de données à l’aide de phpmyadmin

```
rocky@pacman:~/Documents/DevOps$ docker run --name mysql-server \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=db-test \
  -d mysql:5.7
9facdddd67010b8e6ac5ad103e449bf169591ffac426bb20c94600cea1c55133
```

```
rocky@pacman:~/Documents/DevOps$ docker run --name phpmyadmin   -d   --link mysql-server:db   -p 8081:80   -e PMA_HOST=db   phpmyadmin
15238149f1e3350d103b3fbe878985e7d25ab9549eac3f2cf2d3ec0d2fb77606
rocky@pacman:~/Documents/DevOps$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS                  PORTS                                     NAMES
15238149f1e3   phpmyadmin             "/docker-entrypoint.…"   3 seconds ago       Up 3 seconds            0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   phpmyadmin
9facdddd6701   mysql:5.7              "docker-entrypoint.s…"   7 minutes ago       Up 7 minutes            3306/tcp, 33060/tcp                       mysql-server
d33578721bd0   mon-nginx              "/docker-entrypoint.…"   30 minutes ago      Up 30 minutes           0.0.0.0:8085->80/tcp, [::]:8085->80/tcp   custom-nginx
859d2bd59d79   mon-nginx              "/docker-entrypoint.…"   32 minutes ago      Created                                                           nginx-dockerfile
438337c3df45   nginx                  "/docker-entrypoint.…"   About an hour ago   Up About an hour        0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   serveur-web
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago          Exited (0) 9 days ago 
```

[connexion phpmyadmin](./captures/phpmyadmin.png)

La communication entre les deux conteneurs fonctionne grâce à l’option `--link`, qui permet à phpMyAdmin de résoudre le nom `db` vers l’IP du conteneur `mysql-server`.

[ajout data mysql](./captures/données.png)

J'ai rajouté depuis l'interface de phpmyadmin des données tests.

## 8. Faire la même chose que précédemment en utilisant un fichier docker-compose.yml

voir fichier docker-compose.yml (celui dans aucun dossier).

### a. Qu’apporte le fichier docker-compose par rapport aux commandes docker run ? Pourquoi est-il intéressant ? (cf. ce qui a été présenté pendant le cours)

Le fichier `docker-compose.yml` permet de décrire toute l’architecture applicative en un seul fichier. Contrairement à `docker run`, qui nécessite de nombreuses options à taper à chaque fois(ce qui peut être assez pénible), `docker-compose` est **plus lisible, modifiable et versionnable**.

Il devient très facile de partager ou relancer l’environnement de travail complet avec une simple commande : `docker-compose up -d`.

Intérêt du fichier `docker-compose`

- Centralise toute la configuration (services, variables d’environnement, ports…).
- Réduit le risque d’erreur humaine.
- Compatible avec des outils CI/CD (GitLab CI, Jenkins…).
- Gère les dépendances (ex: `depends_on` entre phpMyAdmin et MySQL).
- Permet de **scaler ou isoler plusieurs environnements** de test facilement.
- Permet de décrire l’ensemble des services, réseaux, volumes dans un fichier unique
- Plus simple à lancer, versionner, reproduire

### b. Quel moyen permet de configurer (premier utilisateur, première base de données, mot de passe root, …) facilement le conteneur mysql au lancement ?

Les variables d’environnement permettent de définir :

MYSQL_ROOT_PASSWORD : mot de passe root

MYSQL_DATABASE : base de données initiale

Cela permet de démarrer avec une base prête à l’emploi, sans avoir besoin de se connecter et tout créer à la main.


## 9. Observation de l’isolation réseau entre 3 conteneurs

### a. A l’aide de docker-compose et de l’image praqma/network-multitool disponible sur le Docker Hub créer 3 services (web, app et db) et 2 réseaux (frontend et backend). Les services web et db ne devront pas pouvoir effectuer de ping de l’un vers l’autre

J'ai crée un dossier isolation afin de ne pas mélanger les docker-compose.yml et qu'il récupère celui nécessaire à cette étape.

```
rocky@pacman:~/Documents/DevOps/isolation$ docker compose up -d
WARN[0000] /home/rocky/Documents/DevOps/isolation/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 10/10
 ✔ db Pulled                                                                                                                                                                6.2s 
 ✔ web Pulled                                                                                                                                                               6.2s 
 ✔ app Pulled                                                                                                                                                               6.2s 
[+] Running 5/5
 ✔ Network isolation_frontend  Created                                                                                                                                      0.0s 
 ✔ Network isolation_backend   Created                                                                                                                                      0.0s 
 ✔ Container web               Started                                                                                                                                      0.4s 
 ✔ Container app               Started                                                                                                                                      0.4s 
 ✔ Container db                Started 
 ```

 ```
 rocky@pacman:~/Documents/DevOps/isolation$ docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS              PORTS                                     NAMES
daaeccf4a484   praqma/network-multitool   "/bin/sh /docker/ent…"   About a minute ago   Up About a minute   80/tcp, 443/tcp, 1180/tcp, 11443/tcp      db
c60c9b3bcf40   praqma/network-multitool   "/bin/sh /docker/ent…"   About a minute ago   Up About a minute   80/tcp, 443/tcp, 1180/tcp, 11443/tcp      web
7e8798b76cc6   praqma/network-multitool   "/bin/sh /docker/ent…"   About a minute ago   Up About a minute   80/tcp, 443/tcp, 1180/tcp, 11443/tcp      app
514792e4bb08   phpmyadmin/phpmyadmin      "/docker-entrypoint.…"   7 minutes ago        Up 7 minutes        0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   devops-phpmyadmin-1
7bbb5cb15014   mysql:5.7                  "docker-entrypoint.s…"   7 minutes ago        Up 7 minutes        3306/tcp, 33060/tcp                       devops-mysql-1
```
Je me sers de docker exec afin de tester les pings entre les différents services.
```
rocky@pacman:~/Documents/DevOps/isolation$ docker exec -it web sh
/ # ping db
ping: db: Name does not resolve
/ # exit
rocky@pacman:~/Documents/DevOps/isolation$ docker exec -it app sh
/ # ping web
PING web (172.19.0.3) 56(84) bytes of data.
64 bytes from web.isolation_frontend (172.19.0.3): icmp_seq=1 ttl=64 time=0.031 ms
64 bytes from web.isolation_frontend (172.19.0.3): icmp_seq=2 ttl=64 time=0.035 ms
64 bytes from web.isolation_frontend (172.19.0.3): icmp_seq=3 ttl=64 time=0.064 ms
64 bytes from web.isolation_frontend (172.19.0.3): icmp_seq=4 ttl=64 time=0.068 ms
64 bytes from web.isolation_frontend (172.19.0.3): icmp_seq=5 ttl=64 time=0.054 ms
^C
--- web ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4092ms
rtt min/avg/max/mdev = 0.031/0.050/0.068/0.015 ms
/ # ping db
PING db (172.20.0.2) 56(84) bytes of data.
64 bytes from db.isolation_backend (172.20.0.2): icmp_seq=1 ttl=64 time=0.099 ms
64 bytes from db.isolation_backend (172.20.0.2): icmp_seq=2 ttl=64 time=0.032 ms
64 bytes from db.isolation_backend (172.20.0.2): icmp_seq=3 ttl=64 time=0.051 ms
64 bytes from db.isolation_backend (172.20.0.2): icmp_seq=4 ttl=64 time=0.115 ms
^C
--- db ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3072ms
rtt min/avg/max/mdev = 0.032/0.074/0.115/0.033 ms
```

### b. Quelles lignes du résultat de la commande docker inspect justifient ce comportement ?

```
rocky@pacman:~/Documents/DevOps/isolation$ docker inspect web
[
    {
        "Id": "c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f",
        "Created": "2025-06-03T16:09:13.456474958Z",
        "Path": "/bin/sh",
        "Args": [
            "/docker/entrypoint.sh",
            "/usr/sbin/nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 227732,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2025-06-03T16:09:13.592716479Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:1631e536ed7dd2e8b80119f4f54c7958da9ffc159323ae5ecfb15c76ebdab10a",
        "ResolvConfPath": "/home/rocky/docker/containers/c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f/resolv.conf",
        "HostnamePath": "/home/rocky/docker/containers/c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f/hostname",
        "HostsPath": "/home/rocky/docker/containers/c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f/hosts",
        "LogPath": "/home/rocky/docker/containers/c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f/c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f-json.log",
        "Name": "/web",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "isolation_frontend",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                0,
                0
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": [],
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": null,
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/interrupts",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap",
                "/sys/devices/system/cpu/cpu0/thermal_throttle",
                "/sys/devices/system/cpu/cpu1/thermal_throttle",
                "/sys/devices/system/cpu/cpu2/thermal_throttle",
                "/sys/devices/system/cpu/cpu3/thermal_throttle",
                "/sys/devices/system/cpu/cpu4/thermal_throttle",
                "/sys/devices/system/cpu/cpu5/thermal_throttle",
                "/sys/devices/system/cpu/cpu6/thermal_throttle",
                "/sys/devices/system/cpu/cpu7/thermal_throttle",
                "/sys/devices/system/cpu/cpu8/thermal_throttle",
                "/sys/devices/system/cpu/cpu9/thermal_throttle",
                "/sys/devices/system/cpu/cpu10/thermal_throttle",
                "/sys/devices/system/cpu/cpu11/thermal_throttle",
                "/sys/devices/system/cpu/cpu12/thermal_throttle",
                "/sys/devices/system/cpu/cpu13/thermal_throttle",
                "/sys/devices/system/cpu/cpu14/thermal_throttle",
                "/sys/devices/system/cpu/cpu15/thermal_throttle",
                "/sys/devices/system/cpu/cpu16/thermal_throttle",
                "/sys/devices/system/cpu/cpu17/thermal_throttle",
                "/sys/devices/system/cpu/cpu18/thermal_throttle",
                "/sys/devices/system/cpu/cpu19/thermal_throttle"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "ID": "c60c9b3bcf4067d930d4c3f4eecfcd1e618e145242f51e3fd77549be00b0865f",
                "LowerDir": "/home/rocky/docker/overlay2/2539e3624314e934f18aeb74314db5a8c6032ab03a51565a493faec1371c0576-init/diff:/home/rocky/docker/overlay2/c25c22ba0e074d39af4351845903fc924a7490aca156d93d327a10818e6a4618/diff:/home/rocky/docker/overlay2/12ac369a89ab8fc1238c4681634e816164a7064bae189be310a939958265f89f/diff:/home/rocky/docker/overlay2/606b3d1147b8b7f6d88794c6a115cc7a0bba78e417a4c476ca1d5b50c7144a88/diff:/home/rocky/docker/overlay2/a6323711fa847af6f90fb1de4883ab102214975e64fbebdab1e55f8e579af43c/diff:/home/rocky/docker/overlay2/2b1d39d9b5acb2d7b5713438e560f6ac67a16b4071f0b4a60894655976ee01b1/diff:/home/rocky/docker/overlay2/05bb4d30f7f8ba1d3740af583488bc211f40cb1f804e349890e97218d04e6eed/diff:/home/rocky/docker/overlay2/c95059942e0f196fb46a26c0191caf5f67b0f5ef6ade253f7d22fdd58415bddd/diff",
                "MergedDir": "/home/rocky/docker/overlay2/2539e3624314e934f18aeb74314db5a8c6032ab03a51565a493faec1371c0576/merged",
                "UpperDir": "/home/rocky/docker/overlay2/2539e3624314e934f18aeb74314db5a8c6032ab03a51565a493faec1371c0576/diff",
                "WorkDir": "/home/rocky/docker/overlay2/2539e3624314e934f18aeb74314db5a8c6032ab03a51565a493faec1371c0576/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "c60c9b3bcf40",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": true,
            "AttachStderr": true,
            "ExposedPorts": {
                "11443/tcp": {},
                "1180/tcp": {},
                "443/tcp": {},
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/usr/sbin/nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "praqma/network-multitool",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/bin/sh",
                "/docker/entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "com.docker.compose.config-hash": "fda5dac22c63f9194f0a75529a9b9a6f366e71253c50831a377a27c53797300e",
                "com.docker.compose.container-number": "1",
                "com.docker.compose.depends_on": "",
                "com.docker.compose.image": "sha256:1631e536ed7dd2e8b80119f4f54c7958da9ffc159323ae5ecfb15c76ebdab10a",
                "com.docker.compose.oneoff": "False",
                "com.docker.compose.project": "isolation",
                "com.docker.compose.project.config_files": "/home/rocky/Documents/DevOps/isolation/docker-compose.yml",
                "com.docker.compose.project.working_dir": "/home/rocky/Documents/DevOps/isolation",
                "com.docker.compose.service": "web",
                "com.docker.compose.version": "2.36.2"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "d67ea4bf401291ab7d16402bd72f2ad0eaba165b33fb09e05fb9304916d922fb",
            "SandboxKey": "/var/run/docker/netns/d67ea4bf4012",
            "Ports": {
                "11443/tcp": null,
                "1180/tcp": null,
                "443/tcp": null,
                "80/tcp": null
            },
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "isolation_frontend": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "web",
                        "web"
                    ],
                    "MacAddress": "06:6f:d7:e0:00:30",
                    "DriverOpts": null,
                    "GwPriority": 0,
                    "NetworkID": "7fa40c69b3075d5d4210e6fa1b9bd1905f1e12e728cf4ea8dfcd1f145c8b9f71",
                    "EndpointID": "50217558e500eb262adf7b878bb290b30c60cd65890c47b5550083dc36b2cfc0",
                    "Gateway": "172.19.0.1",
                    "IPAddress": "172.19.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": [
                        "web",
                        "c60c9b3bcf40"
                    ]
                }
            }
        }
    }
]
```

Pour la partie web: **"NetworkMode": "isolation_frontend"**

```
rocky@pacman:~/Documents/DevOps/isolation$ docker inspect db
[
    {
        "Id": "daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833",
        "Created": "2025-06-03T16:09:13.456864309Z",
        "Path": "/bin/sh",
        "Args": [
            "/docker/entrypoint.sh",
            "/usr/sbin/nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 227733,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2025-06-03T16:09:13.593963712Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:1631e536ed7dd2e8b80119f4f54c7958da9ffc159323ae5ecfb15c76ebdab10a",
        "ResolvConfPath": "/home/rocky/docker/containers/daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833/resolv.conf",
        "HostnamePath": "/home/rocky/docker/containers/daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833/hostname",
        "HostsPath": "/home/rocky/docker/containers/daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833/hosts",
        "LogPath": "/home/rocky/docker/containers/daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833/daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833-json.log",
        "Name": "/db",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "isolation_backend",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                0,
                0
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": [],
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": null,
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/interrupts",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap",
                "/sys/devices/system/cpu/cpu0/thermal_throttle",
                "/sys/devices/system/cpu/cpu1/thermal_throttle",
                "/sys/devices/system/cpu/cpu2/thermal_throttle",
                "/sys/devices/system/cpu/cpu3/thermal_throttle",
                "/sys/devices/system/cpu/cpu4/thermal_throttle",
                "/sys/devices/system/cpu/cpu5/thermal_throttle",
                "/sys/devices/system/cpu/cpu6/thermal_throttle",
                "/sys/devices/system/cpu/cpu7/thermal_throttle",
                "/sys/devices/system/cpu/cpu8/thermal_throttle",
                "/sys/devices/system/cpu/cpu9/thermal_throttle",
                "/sys/devices/system/cpu/cpu10/thermal_throttle",
                "/sys/devices/system/cpu/cpu11/thermal_throttle",
                "/sys/devices/system/cpu/cpu12/thermal_throttle",
                "/sys/devices/system/cpu/cpu13/thermal_throttle",
                "/sys/devices/system/cpu/cpu14/thermal_throttle",
                "/sys/devices/system/cpu/cpu15/thermal_throttle",
                "/sys/devices/system/cpu/cpu16/thermal_throttle",
                "/sys/devices/system/cpu/cpu17/thermal_throttle",
                "/sys/devices/system/cpu/cpu18/thermal_throttle",
                "/sys/devices/system/cpu/cpu19/thermal_throttle"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "ID": "daaeccf4a48453f4d5b3a9a1a5af7101ab478fe14d3032cbf5a73d487f9bb833",
                "LowerDir": "/home/rocky/docker/overlay2/12a756fd25124654704b2987701fea87bd556e01a072f076e18e88896dceb72a-init/diff:/home/rocky/docker/overlay2/c25c22ba0e074d39af4351845903fc924a7490aca156d93d327a10818e6a4618/diff:/home/rocky/docker/overlay2/12ac369a89ab8fc1238c4681634e816164a7064bae189be310a939958265f89f/diff:/home/rocky/docker/overlay2/606b3d1147b8b7f6d88794c6a115cc7a0bba78e417a4c476ca1d5b50c7144a88/diff:/home/rocky/docker/overlay2/a6323711fa847af6f90fb1de4883ab102214975e64fbebdab1e55f8e579af43c/diff:/home/rocky/docker/overlay2/2b1d39d9b5acb2d7b5713438e560f6ac67a16b4071f0b4a60894655976ee01b1/diff:/home/rocky/docker/overlay2/05bb4d30f7f8ba1d3740af583488bc211f40cb1f804e349890e97218d04e6eed/diff:/home/rocky/docker/overlay2/c95059942e0f196fb46a26c0191caf5f67b0f5ef6ade253f7d22fdd58415bddd/diff",
                "MergedDir": "/home/rocky/docker/overlay2/12a756fd25124654704b2987701fea87bd556e01a072f076e18e88896dceb72a/merged",
                "UpperDir": "/home/rocky/docker/overlay2/12a756fd25124654704b2987701fea87bd556e01a072f076e18e88896dceb72a/diff",
                "WorkDir": "/home/rocky/docker/overlay2/12a756fd25124654704b2987701fea87bd556e01a072f076e18e88896dceb72a/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "daaeccf4a484",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": true,
            "AttachStderr": true,
            "ExposedPorts": {
                "11443/tcp": {},
                "1180/tcp": {},
                "443/tcp": {},
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/usr/sbin/nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "praqma/network-multitool",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/bin/sh",
                "/docker/entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "com.docker.compose.config-hash": "d6294aaf2a7cc18b03c5fb167463d46e8fc3365eefa405dc55bd9b1a3dc02b3b",
                "com.docker.compose.container-number": "1",
                "com.docker.compose.depends_on": "",
                "com.docker.compose.image": "sha256:1631e536ed7dd2e8b80119f4f54c7958da9ffc159323ae5ecfb15c76ebdab10a",
                "com.docker.compose.oneoff": "False",
                "com.docker.compose.project": "isolation",
                "com.docker.compose.project.config_files": "/home/rocky/Documents/DevOps/isolation/docker-compose.yml",
                "com.docker.compose.project.working_dir": "/home/rocky/Documents/DevOps/isolation",
                "com.docker.compose.service": "db",
                "com.docker.compose.version": "2.36.2"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "f22e1296c69f89a39760f919ef9a3a9465562b7f5a1b5af965d2eeb5a55875e2",
            "SandboxKey": "/var/run/docker/netns/f22e1296c69f",
            "Ports": {
                "11443/tcp": null,
                "1180/tcp": null,
                "443/tcp": null,
                "80/tcp": null
            },
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "isolation_backend": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "db",
                        "db"
                    ],
                    "MacAddress": "de:67:7e:c3:e0:6f",
                    "DriverOpts": null,
                    "GwPriority": 0,
                    "NetworkID": "2c05f0d1624f7cddf4879b221a8bcf1f68abb618b02d1aed3446efc284f98733",
                    "EndpointID": "32ebb6c9b4d020def296574332cfc43683d2bd22b932c160dfe818d4572270e6",
                    "Gateway": "172.20.0.1",
                    "IPAddress": "172.20.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": [
                        "db",
                        "daaeccf4a484"
                    ]
                }
            }
        }
    }
]
```
Pour la partie db: **"NetworkMode": "isolation_backend"**

La commande docker inspect <container> montre que web est uniquement sur frontend, db uniquement sur backend, et app sur les deux réseaux.

Pour la partie app:
```
"Networks": {
                "isolation_backend": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "app",
                        "app"
                    ],
                    "MacAddress": "",
                    "DriverOpts": null,
                    "GwPriority": 0,
                    "NetworkID": "2c05f0d1624f7cddf4879b221a8bcf1f68abb618b02d1aed3446efc284f98733",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": [
                        "app",
                        "7e8798b76cc6"
                    ]
                },
                "isolation_frontend": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "app",
                        "app"
                    ],
                    "MacAddress": "",
                    "DriverOpts": null,
                    "GwPriority": 0,
                    "NetworkID": "7fa40c69b3075d5d4210e6fa1b9bd1905f1e12e728cf4ea8dfcd1f145c8b9f71",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": [
                        "app",
                        "7e8798b76cc6"
```
(code non entier, ici seulement **"Networks"** nous intéresse, on voit bien qu'il y a **"isolation_frontend"** et **"isolation_backend"**)

### c. Dans quelle situation réelles (avec quelles images) pourrait-on avoir cette configuration réseau ? Dans quel but ?

- web : interface front-end (exposé publiquement)

- app : logique métier (API intermédiaire)

- db : base de données protégée (accès privé uniquement)

```
rocky@pacman:~/Documents/DevOps$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED        STATUS                    PORTS                                     NAMES
daaeccf4a484   praqma/network-multitool   "/bin/sh /docker/ent…"   15 hours ago   Exited (0) 14 hours ago                                             db
c60c9b3bcf40   praqma/network-multitool   "/bin/sh /docker/ent…"   15 hours ago   Exited (0) 14 hours ago                                             web
7e8798b76cc6   praqma/network-multitool   "/bin/sh /docker/ent…"   15 hours ago   Exited (0) 14 hours ago                                             app
514792e4bb08   phpmyadmin/phpmyadmin      "/docker-entrypoint.…"   15 hours ago   Up 20 minutes             0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   devops-phpmyadmin-1
7bbb5cb15014   mysql:5.7                  "docker-entrypoint.s…"   15 hours ago   Up 20 minutes             3306/tcp, 33060/tcp                       devops-mysql-1
d33578721bd0   mon-nginx                  "/docker-entrypoint.…"   41 hours ago   Exited (0) 40 hours ago                                             custom-nginx
859d2bd59d79   mon-nginx                  "/docker-entrypoint.…"   41 hours ago   Created                                                             nginx-dockerfile
438337c3df45   nginx                      "/docker-entrypoint.…"   42 hours ago   Exited (0) 40 hours ago                                             serveur-web
69c7efef5bae   nwodtuhs/exegol:full       "/bin/bash /.exegol/…"   11 days ago    Exited (0) 11 days ago                                              exegol-STHACK
rocky@pacman:~/Documents/DevOps$ 
```

Cette configuration permet de simuler une DMZ (zone démilitarisée) où seuls certains services ont accès à d'autres, renforçant ainsi la sécurité réseau.

On pourrait avoir un conteneur NGINX pour servir l’interface utilisateur, un conteneur Node.js ou Python Flask pour la logique métier, et un conteneur MySQL pour stocker les données. Dans ce cas, le front n’a pas besoin de parler directement à la base de données, et ne doit même pas pouvoir le faire pour des raisons de sécurité. C’est le back-end (API) qui joue le rôle d’intermédiaire et contrôle tous les accès à la base.

Dans Docker, on crée donc deux réseaux : un réseau frontend, partagé par le front et l’API, et un réseau backend, partagé par l’API et la base de données. Comme ça, le front peut parler à l’API, l’API peut parler à la base, mais le front ne peut jamais accéder à la base directement.

(voir les ping entre les differents services)