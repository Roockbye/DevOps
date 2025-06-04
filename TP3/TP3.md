## 2. Initialisez un projet React vierge

## 3. Vérifiez que l'application fonctionne correctement en local

## 4. Créez un fichier Dockerfile à la racine du projet. Celui-ci doit se diviser en deux grandes parties :

### a. Étape 1 : Construction de l'application React FROM node:18-alpine as build

### b. Étape 2 : Serveur web pour l'application FROM nginx:alpine

## 5. Instanciez l’image créé précédemment afin d’observer le même résultat que lors de la question 3