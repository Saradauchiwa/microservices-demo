# Déployer Online Boutique avec Kustomize

Ce dépôt utilise [Kustomize](https://kustomize.io/) pour gérer les manifestes Kubernetes de l'application Online Boutique.

L'objectif du projet est de préparer un déploiement local sur un cluster Kubernetes composé d'un nœud de contrôle et de deux nœuds workers.

## 1. Prérequis

Avant de déployer l'application, les éléments suivants doivent être disponibles :

- Un cluster Kubernetes opérationnel.
- `kubectl` configuré pour communiquer avec le cluster.
- Un registre Docker privé accessible depuis les nœuds Kubernetes.
- Les images des microservices construites et publiées dans ce registre.

La construction des images et leur analyse de sécurité sont prévues dans le pipeline DevSecOps.

## 2. Structure du répertoire

- `base/` : contient les manifestes Kubernetes des microservices et de Redis.
- `components/` : contient les personnalisations réutilisables des manifestes.
- `tests/` : répertoire réservé aux futurs scénarios de validation des composants Kustomize.

## 3. Vérifier les manifestes

Depuis la racine du dépôt, exécuter :

```bash
kubectl kustomize kustomize/
```

Cette commande génère les manifestes Kubernetes sans les appliquer au cluster.

Pour enregistrer le résultat dans un fichier :

```bash
kubectl kustomize kustomize/ > manifests.yaml
```

## 4. Composants disponibles

Les composants conservés dans ce projet sont les suivants :

- **non-public-frontend** : supprime le Service `frontend-external` afin d'éviter l'exposition publique directe du frontend.
- **network-policies** : permet de définir des règles de communication réseau entre les composants de l'application. Son activation dépend de la configuration réseau du cluster.
- **container-images-tag** : permet de définir le tag des images des microservices.
- **container-images-tag-suffix** : permet d'ajouter un suffixe aux tags des images.
- **container-images-registry** : permet de remplacer les registres d'images par le registre privé local.

Les composants liés au registre et aux tags seront activés lorsque le registre local et les conventions de nommage des images seront configurés.

## 5. Configuration des composants

Les composants actifs sont déclarés dans `kustomize/kustomization.yaml`.

Exemple de configuration actuelle :

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - base

components:
  - components/non-public-frontend
```

Les autres composants pourront être ajoutés à cette configuration après leur paramétrage.

## 6. Déployer l'application

Une fois le cluster opérationnel, les images disponibles dans le registre privé et les manifestes configurés, le déploiement pourra être effectué avec :

```bash
kubectl apply -k kustomize/
```

Vérifier ensuite l'état des ressources :

```bash
kubectl get pods -A
```

Pour afficher les services :

```bash
kubectl get services
```

## 7. Accéder au frontend

Le composant `non-public-frontend` supprime le Service `frontend-external`.

L'accès au frontend devra donc être configuré selon l'architecture réseau retenue pour le cluster, par exemple au moyen d'un accès interne ou d'un Ingress.

## 8. Intégration DevSecOps

Les manifestes Kubernetes constituent la dernière partie du processus de livraison.

Le pipeline DevSecOps prévu suit les étapes suivantes :

1. Récupération du code source depuis GitHub.
2. Analyse de la qualité du code avec SonarQube.
3. Construction des images Docker.
4. Analyse de sécurité des images avec Trivy.
5. Publication des images dans le registre privé local.
6. Déploiement sur le cluster Kubernetes.