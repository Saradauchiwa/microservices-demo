# Workflows GitHub Actions

Cette page présente les workflows GitHub Actions utilisés pour effectuer des vérifications automatiques sur le dépôt du projet.

Les GitHub Actions permettent de contrôler la qualité et la cohérence des fichiers du projet. Le pipeline principal de CI/CD sera, quant à lui, réalisé avec Jenkins.

## Infrastructure

Les workflows GitHub Actions s'exécutent sur des runners Ubuntu hébergés par GitHub.

L'environnement de déploiement cible est une infrastructure Kubernetes locale composée de trois machines virtuelles :
- Un nœud control plane.
- Deux nœuds workers.

Les outils Jenkins, SonarQube, Trivy et le registre privé local seront hébergés sur une quatrième machine virtuelle.

## Workflows

### Tests du code — [ci-pr.yaml](ci-pr.yaml)

Ce workflow exécute des tests automatisés lors des événements définis dans sa configuration.

Il permet notamment de vérifier le fonctionnement de certains composants du projet à l'aide de tests unitaires.

### Validation des manifestes Kubernetes — [kubevious-manifests-ci.yaml](kubevious-manifests-ci.yaml)

Ce workflow vérifie les manifestes Kubernetes présents dans les répertoires `kubernetes-manifests/` et `kustomize/`.

Il permet de détecter certaines erreurs de configuration avant l'utilisation des manifestes dans l'environnement Kubernetes local.

### Validation de la configuration Kustomize — [kustomize-build-ci.yaml](kustomize-build-ci.yaml)

Ce workflow vérifie que la configuration Kustomize principale peut être générée correctement.

Il utilise la commande :

```bash
kubectl kustomize kustomize/
```

Cette vérification permet de détecter les erreurs de génération des ressources Kubernetes.

## Pipeline DevSecOps avec Jenkins

Le pipeline DevSecOps sera mis en place séparément avec Jenkins.

Il aura pour objectif d'automatiser les étapes suivantes :

1. Récupération du code source depuis GitHub.
2. Analyse de la qualité du code avec SonarQube.
3. Construction des images Docker.
4. Analyse des images avec Trivy.
5. Publication des images validées dans le registre privé local.
6. Déploiement des applications sur le cluster Kubernetes local.

Les GitHub Actions et Jenkins ont donc des rôles complémentaires : les premières assurent des vérifications sur le dépôt, tandis que Jenkins prendra en charge le futur processus d'intégration et de déploiement DevSecOps.