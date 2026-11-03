# Exigences du projet

Ce document présente les exigences que chaque modification apportée au dépôt doit respecter.

Chaque modification doit :

1. Préserver le parcours utilisateur principal de l'application Online Boutique.
2. Préserver la simplicité de l'application et de ses démonstrations.
3. Faciliter le déploiement de l'application dans un environnement Kubernetes local.

Ces exigences concernent la configuration par défaut de l'application Online Boutique.

Toute modification susceptible de compromettre ces exigences ne doit pas être intégrée directement à la configuration par défaut. Elle doit être facultative et activable uniquement lorsque cela est nécessaire, idéalement sous la forme d'un composant Kustomize, lorsqu'elle correspond à l'objectif du projet.

## 1. Préserver le parcours utilisateur des débutants Kubernetes

L'application Online Boutique doit rester accessible aux développeurs qui découvrent Kubernetes.

Un utilisateur doit pouvoir déployer la configuration par défaut de l'application sur un cluster Kubernetes local, sans dépendre d'un fournisseur de services cloud particulier.

Le projet doit rester accessible et reproductible dans un environnement local.

La configuration proposée doit notamment permettre de comprendre les principales ressources Kubernetes utilisées par l'application, telles que les Deployments, les Services et les Pods.

## 2. Préserver la simplicité des démonstrations

Les nouvelles modifications ne doivent pas compliquer le parcours utilisateur présenté dans les démonstrations et les tutoriels.

Le parcours principal de l'application est le suivant :

1. Accéder à Online Boutique depuis un navigateur web.
2. Sélectionner un article sur la page d'accueil et l'ajouter au panier.
3. Remplir le formulaire de commande, qui contient des données de démonstration préremplies, notamment l'adresse de livraison.
4. Valider la commande et finaliser l'achat.

Les nouvelles fonctionnalités doivent préserver ce parcours et ne pas dégrader le fonctionnement des microservices existants.

## 3. Préserver la simplicité du déploiement local

Les nouvelles modifications ne doivent pas ajouter de complexité inutile au processus de déploiement de l'application dans notre infrastructure Kubernetes locale.

En particulier, elles ne doivent pas imposer des outils ou des étapes supplémentaires sans justification technique.

Les extensions de l'application, telles que l'ajout d'un nouveau microservice ou d'une nouvelle fonctionnalité, doivent être intégrées de manière modulaire.

Dans la mesure du possible, les composants Kustomize doivent être utilisés pour permettre l'activation facultative de ces extensions.

## 4. Respecter les exigences de la plateforme DevSecOps

Toute modification apportée au projet doit également respecter les exigences de sécurité et de qualité de la plateforme DevSecOps.

Les modifications doivent notamment :

- Respecter les bonnes pratiques de conteneurisation avec Docker.
- Faire l'objet d'une analyse de qualité du code avec SonarQube lorsque cela est applicable.
- Faire l'objet d'une analyse de sécurité des images avec Trivy.
- Utiliser le registre privé local pour les images validées.
- Respecter les configurations de sécurité définies dans les manifestes Kubernetes.

L'objectif est de préserver la simplicité de l'application tout en garantissant un processus de livraison sécurisé et reproductible.