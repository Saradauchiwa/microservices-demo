# Ajouter un nouveau microservice

Ce document présente les étapes nécessaires pour ajouter un nouveau microservice à l'application Online Boutique.

## 1. Créer un répertoire

Créer un nouveau répertoire dans le dossier `src/`. Le nom du répertoire doit correspondre à celui du microservice.

## 2. Ajouter le code source

Placer le code source du microservice dans le nouveau répertoire, en respectant les conventions utilisées par les microservices existants.

Par exemple, un microservice développé en Python peut contenir les fichiers suivants :

- `README.md` : description et documentation du service.
- `main.py` : point d'entrée de l'application.
- `requirements.in` : liste des dépendances Python.
- `Dockerfile` : instructions nécessaires à la construction de l'image Docker.

Il est recommandé de consulter les microservices existants pour s'inspirer de leur organisation.

## 3. Créer un Dockerfile

Créer un fichier `Dockerfile` dans le répertoire du microservice.

Ce fichier définit les différentes instructions nécessaires à la construction de son image Docker.

Il doit être adapté au langage de programmation, aux dépendances et aux besoins du microservice.

## 4. Créer les manifestes Kubernetes

Ajouter les manifestes Kubernetes du nouveau microservice dans le répertoire `kustomize/base/`.

Les ressources nécessaires comprennent généralement :

- **Deployment** : permet de gérer les Pods du microservice.
- **Service** : permet d'assurer la communication entre ce microservice et les autres composants de l'application.

Respecter les conventions de nommage et les configurations de sécurité utilisées dans les manifestes existants.

L'image indiquée dans le Deployment doit correspondre à celle construite et publiée par le pipeline DevSecOps.

## 5. Mettre à jour la configuration Kustomize

Déclarer les nouveaux manifestes dans le fichier `kustomize/base/kustomization.yaml` afin qu'ils soient pris en compte lors de la génération des ressources Kubernetes.

Si le microservice nécessite une configuration particulière, vérifier si un composant Kustomize existant peut être réutilisé.

## 6. Intégrer le microservice au pipeline DevSecOps

Adapter le pipeline Jenkins pour prendre en compte le nouveau microservice dans les étapes concernées.

Le processus doit notamment intégrer :

- La récupération du code source depuis GitHub.
- L'analyse de la qualité du code avec SonarQube.
- La construction de l'image Docker.
- L'analyse de sécurité de l'image avec Trivy.
- La publication de l'image dans le registre privé local.
- Le déploiement sur le cluster Kubernetes.

## 7. Mettre à jour la documentation

Mettre à jour la documentation du projet afin de présenter le nouveau microservice et ses fonctionnalités.

Cela peut notamment comprendre :

- La mise à jour du fichier `README.md` principal.
- L'actualisation des schémas d'architecture dans `docs/img/`.
- L'ajout d'un document spécifique si le microservice apporte de nouvelles fonctionnalités.