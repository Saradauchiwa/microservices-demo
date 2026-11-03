# Sécurisation du cluster avec les NetworkPolicies

Les NetworkPolicies Kubernetes permettent de contrôler les communications réseau entre les Pods et les Services du cluster.

Elles contribuent à renforcer la sécurité en limitant les communications aux flux nécessaires au fonctionnement des microservices.

## Prérequis

L'application des NetworkPolicies nécessite un plugin CNI (Container Network Interface) qui prend en charge leur application.

Dans notre environnement Kubernetes local, le choix et la configuration du CNI devront être réalisés avant l'activation de ce composant.

Le simple fait de créer des ressources NetworkPolicy ne garantit pas leur application si le CNI ne prend pas en charge cette fonctionnalité.

## Déploiement avec Kustomize

Ce composant permet d'intégrer des NetworkPolicies à la plateforme Online Boutique. Il prévoit des règles de filtrage réseau associées aux différents microservices.

**Attention :** ce composant reste désactivé dans notre configuration principale tant que le CNI n'a pas été choisi et configuré.

Après validation du CNI, depuis le répertoire `kustomize/`, il sera possible de l'ajouter avec la commande `kustomize edit add component components/network-policies`.

Les manifests peuvent ensuite être générés avec `kubectl kustomize .` et déployés avec `kubectl apply -k .`.

## Vérification

Pour vérifier les NetworkPolicies présentes dans le cluster : `kubectl get networkpolicies`.

Pour examiner une règle particulière : `kubectl describe networkpolicy NOM_DE_LA_POLICY`.

## Points de vigilance

- Vérifier que le CNI prend en charge les NetworkPolicies.
- Examiner les règles d'entrée (Ingress) et de sortie (Egress) avant leur activation.
- Vérifier que les communications indispensables entre microservices restent autorisées.
- Tester les règles après leur déploiement afin d'éviter de bloquer des fonctionnalités de l'application.

## Ressources utiles

- [Documentation Kubernetes : Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [Exemples de règles NetworkPolicy](https://github.com/ahmetb/kubernetes-network-policy-recipes)
