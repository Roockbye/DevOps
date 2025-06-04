# TP Docker – Bachelor 3 Informatique

## Objectif du TP

Ce projet a pour objectif de découvrir, installer et manipuler Docker et Docker Compose en construisant progressivement une infrastructure composée de conteneurs applicatifs, web et base de données, avec un focus sur l’orchestration, les volumes, la persistance et l’isolation réseau.

## Structure du projet

- index.html : page web test pour nginx

- Dockerfile : fichier de build pour l’image nginx personnalisée

- docker-compose.yml : orchestration des services MySQL + PhpMyAdmin et des réseaux pour multitool

- TP1.md : compte-rendu détaillé de chaque étape du TP1

- captures/ : dossier contenant les captures d’écran

## Lancer les conteneurs
Serveur web avec image personnalisée
```
docker build -t nginx .
docker run --name nginx -d -p 8085:80 nginx
```
Accès : http://localhost:8085

MySQL + PhpMyAdmin avec docker-compose
```
docker compose up -d
```
Accès PhpMyAdmin : http://localhost:8080

Utilisateur : root / Mot de passe : root


Puis utiliser docker exec pour tester les pings entre conteneurs.

## Rapport

Le fichier TP1.md contient un compte-rendu structuré et détaillé de toutes les étapes du TP1 (installation, commandes, serveurs, volumes, build d’image, base de données, docker-compose, isolation réseau…). De même pour la suite des autres TPs.
