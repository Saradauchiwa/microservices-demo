# Plateforme DevSecOps sécurisée — Online Boutique

## Présentation du projet

Ce projet consiste à adapter l'application **Online Boutique**, initialement développée par Google, afin de concevoir et de mettre en œuvre une plateforme DevSecOps locale dédiée à l'automatisation et à la sécurisation du déploiement d'applications conteneurisées.

Online Boutique est une application web de commerce électronique qui permet aux utilisateurs de parcourir un catalogue de produits, d'ajouter des articles à leur panier et de simuler une commande.

L'application repose sur une architecture distribuée composée de 11 microservices développés dans différents langages et communiquant entre eux à l'aide de gRPC.

## Objectifs du projet

L'objectif principal est de mettre en place une chaîne DevSecOps permettant d'intégrer des contrôles de qualité et de sécurité tout au long du processus de déploiement.

Les objectifs spécifiques sont les suivants :

- Conteneuriser les différents microservices avec Docker.
- Automatiser les étapes d'intégration et de déploiement avec Jenkins.
- Analyser la qualité du code source avec SonarQube.
- Détecter les vulnérabilités des images conteneurisées avec Trivy.
- Centraliser les images Docker dans un registre privé local.
- Déployer les applications sur un cluster Kubernetes local.
- Renforcer la sécurité des ressources et des déploiements Kubernetes.

## Architecture de l'application

Online Boutique est composée de 11 microservices :

| Microservice | Langage | Rôle |
|---|---|---|
| [frontend](src/frontend) | Go | Expose l'interface web de l'application. |
| [cartservice](src/cartservice) | C# | Gère le panier des utilisateurs avec Redis. |
| [productcatalogservice](src/productcatalogservice) | Go | Fournit le catalogue des produits et permet leur recherche. |
| [currencyservice](src/currencyservice) | Node.js | Effectue les conversions de devises. |
| [paymentservice](src/paymentservice) | Node.js | Simule le traitement des paiements. |
| [shippingservice](src/shippingservice) | Go | Calcule les frais de livraison et simule l'expédition. |
| [emailservice](src/emailservice) | Python | Simule l'envoi des confirmations de commande. |
| [checkoutservice](src/checkoutservice) | Go | Coordonne le processus de commande. |
| [recommendationservice](src/recommendationservice) | Python | Propose des produits en fonction du panier. |
| [adservice](src/adservice) | Java | Fournit des publicités contextuelles. |
| [loadgenerator](src/loadgenerator) | Python / Locust | Génère du trafic pour simuler le comportement des utilisateurs. |

Redis est également utilisé pour le stockage des paniers.

Le diagramme ci-dessous présente l'architecture fonctionnelle de l'application.

![Architecture des microservices](docs/img/architecture-diagram.png)

Les descriptions Protocol Buffers sont disponibles dans le répertoire [protos](protos).

## Architecture de l'infrastructure DevSecOps

L'infrastructure cible repose sur quatre machines virtuelles hébergées dans un environnement local.

| Machine virtuelle | Rôle | Composants |
|---|---|---|
| VM 1 | Intégration et sécurité | Docker Engine, Jenkins, SonarQube, Trivy et registre privé local. |
| VM 2 | Kubernetes Control Plane | Gestion du cluster Kubernetes. |
| VM 3 | Kubernetes Worker 1 | Exécution des applications conteneurisées. |
| VM 4 | Kubernetes Worker 2 | Exécution des applications conteneurisées. |

Le moteur Docker sera utilisé pour construire les images. Le cluster Kubernetes utilisera son propre environnement d'exécution de conteneurs.

## Pipeline DevSecOps cible

Le processus d'automatisation envisagé suit les étapes suivantes :

1. **Récupération du code source :** Jenkins récupère le code depuis le dépôt GitHub.
2. **Analyse de qualité :** SonarQube examine le code source et détecte les problèmes de qualité.
3. **Construction :** Docker construit les images des microservices.
4. **Analyse de sécurité :** Trivy analyse les images à la recherche de vulnérabilités.
5. **Publication :** les images validées sont envoyées vers le registre privé local.
6. **Déploiement :** Jenkins déclenche le déploiement des applications sur le cluster Kubernetes.

## Technologies utilisées

| Domaine | Technologies |
|---|---|
| Gestion du code source | Git, GitHub |
| Automatisation CI/CD | Jenkins |
| Conteneurisation | Docker |
| Analyse de qualité | SonarQube |
| Analyse de vulnérabilités | Trivy |
| Orchestration | Kubernetes |
| Gestion des manifestes | Kustomize |
| Registre d'images | Docker Registry |
| Tests et validation | GitHub Actions, kubectl |

## Organisation du dépôt

```text
microservices-demo/
├── .github/
│   └── workflows/           # Workflows GitHub Actions
├── docs/                    # Documentation du projet
├── kubernetes-manifests/    # Manifestes Kubernetes
├── kustomize/
│   ├── base/                 # Ressources Kubernetes de base
│   ├── components/           # Composants de personnalisation
│   └── tests/                # Scénarios de validation
├── protos/                  # Descriptions Protocol Buffers
├── src/                     # Code source des microservices
└── README.md
```

## Prérequis

Pour travailler sur le projet, les outils suivants sont nécessaires :

- Git pour la gestion du code source.
- Docker Engine pour la construction des images.
- Jenkins pour l'automatisation du pipeline.
- SonarQube pour l'analyse de qualité.
- Trivy pour l'analyse de sécurité.
- Kubernetes pour l'orchestration des conteneurs.
- kubectl pour administrer le cluster.
- Kustomize pour gérer les manifestes Kubernetes.

## Vérification de la configuration Kubernetes

La configuration Kustomize peut être générée avec la commande suivante :

```bash
kubectl kustomize kustomize/
```

Cette commande permet de vérifier la génération des ressources Kubernetes avant leur déploiement.

Le déploiement sur le cluster sera effectué après la configuration du registre privé local et des références d'images correspondantes.

Pour les étapes détaillées de préparation de l'environnement et de déploiement, consulter le [guide de développement](docs/development-guide.md).

## Documentation

- [Guide de développement](docs/development-guide.md)
- [Objectif du projet](docs/purpose.md)
- [Exigences du projet](docs/product-requirements.md)
- [Configuration Kustomize](kustomize/README.md)
- [Ajout d'un microservice](docs/adding-new-microservice.md)
- [Workflows GitHub Actions](.github/workflows/README.md)

## Captures d'écran

| Page d'accueil | Écran de commande |
|---|---|
| ![Page d'accueil](docs/img/online-boutique-frontend-1.png) | ![Écran de commande](docs/img/online-boutique-frontend-2.png) |

## Origine du projet

Ce projet est une adaptation du dépôt [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo).

Les composants liés aux services cloud et aux solutions de déploiement non retenues ont été écartés afin de privilégier une infrastructure locale et une démarche DevSecOps centrée sur la qualité, la sécurité et l'automatisation.
