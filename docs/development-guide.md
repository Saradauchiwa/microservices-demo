# Guide de développement

Ce document présente les étapes nécessaires pour construire, vérifier et déployer l'application Online Boutique dans un environnement Kubernetes local à l'aide de la plateforme DevSecOps.

## 1. Présentation de l'architecture

Le projet repose sur une infrastructure locale composée de quatre machines virtuelles :

- **VM CI/CD** : héberge Jenkins, Docker Engine, SonarQube, Trivy et le registre privé d'images.
- **VM Kubernetes Control Plane** : assure la gestion et l'orchestration du cluster Kubernetes.
- **VM Kubernetes Worker 1** : héberge une partie des applications conteneurisées.
- **VM Kubernetes Worker 2** : héberge une autre partie des applications conteneurisées.

Le code source de l'application est hébergé sur GitHub.

## 2. Prérequis

Avant de commencer, les éléments suivants doivent être disponibles :

- Git pour la gestion du code source.
- Docker Engine pour la construction des images.
- Jenkins pour l'automatisation du pipeline.
- SonarQube pour l'analyse de la qualité du code.
- Trivy pour l'analyse de sécurité des images.
- Un registre privé local pour stocker les images.
- Un cluster Kubernetes opérationnel composé d'un nœud de contrôle et de deux nœuds workers.
- `kubectl` configuré pour communiquer avec le cluster.

Cloner le dépôt du projet :

```bash
git clone https://github.com/pepiteDK/microservices-demo.git
cd microservices-demo
```

## 3. Vérifier le cluster Kubernetes

Depuis une machine disposant d'un accès au cluster, exécuter :

```bash
kubectl get nodes
```

Vérifier que le nœud de contrôle et les deux nœuds workers sont disponibles et dans l'état `Ready`.

## 4. Construire les images de l'application

L'application Online Boutique est composée de plusieurs microservices indépendants.

Chaque microservice dispose de son propre code source et de son Dockerfile dans le répertoire `src/`.

La construction des images est réalisée par le pipeline Jenkins.

Le processus comprend les étapes suivantes :

1. Récupération du code source depuis GitHub.
2. Analyse de la qualité du code avec SonarQube.
3. Construction des images Docker.
4. Analyse de sécurité des images avec Trivy.
5. Publication des images validées dans le registre privé local.

## 5. Vérifier les manifestes Kubernetes

Depuis la racine du dépôt, exécuter :

```bash
kubectl kustomize kustomize/
```

Cette commande génère les manifestes Kubernetes sans les appliquer au cluster.

Elle permet de vérifier la configuration avant le déploiement.

## 6. Déployer l'application

Lorsque les images sont disponibles dans le registre privé et que les manifestes sont correctement configurés, déployer l'application avec :

```bash
kubectl apply -k kustomize/
```

Vérifier ensuite l'état des Pods :

```bash
kubectl get pods -A
```

Afficher les services :

```bash
kubectl get services
```

## 7. Accéder au frontend

Le frontend n'est pas exposé par un Service de type LoadBalancer public.

Son accès doit respecter la configuration réseau retenue pour le cluster Kubernetes.

Pour un accès temporaire depuis une machine disposant de `kubectl`, utiliser la redirection de port :

```bash
kubectl get service frontend
```

Cette commande permet de vérifier le port exposé par le Service avant de configurer la redirection :

```bash
kubectl port-forward service/frontend 8080:<PORT_SERVICE>
```

Remplacer `<PORT_SERVICE>` par le port réellement déclaré dans le Service.

L'application sera alors accessible à l'adresse :

`http://localhost:8080`

## 8. Ajouter un nouveau microservice

Pour intégrer un nouveau microservice à l'application, consulter le guide [Ajouter un nouveau microservice](adding-new-microservice.md).

## 9. Supprimer les ressources déployées

Pour supprimer les ressources Kubernetes gérées par cette configuration Kustomize, exécuter :

```bash
kubectl delete -k kustomize/
```

Cette commande supprime les ressources déclarées dans la configuration Kustomize du projet.