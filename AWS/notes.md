# AWS Notes

## IAM
- **Policies** : Pour Users et Groupes
- **Roles** : Pour Services

---
## EC2
### Option achat
- **On demand** : Arrive quand on veut à l'hôtel, mais on paie plein tarif.
- **Reserved** : Réserve à l'avance pour une longue durée et on obtient une réduction.
- **Savings Plans** : Paie un montant fixe par heure pendant une certaine durée, peu importe le type de chambre.
- **Instances Spot** : Prends sur les chambres vides à petit prix, l'hôtel peut nous demander de partir à tout moment.
- **Dedicated host** : Réserve tout un bâtiment de l'hôtel.
- **Capacity reservations** : Réserve une chambre à plein tarif pour une durée déterminée, même si on ne l'utilise pas.

### Placement Group
- **Cluster** : Groupe d'instances dans une même AZ pour faible latence.
- **Spread** : Instances réparties sur différents matériels (max 7 par AZ).
- **Partition** : Instances réparties par partitions indépendantes (jusqu'à des centaines par AZ, idéal pour Hadoop, Cassandra, Kafka).

---
## High availabity & Scalabilty
⚠️ GENEVE protocol port 6081 => Gateway Load Balancer

---
## CloudFront & AWS Global Accelerator

### CloudFront vs S3 Cross Region Replication

| Critères               | CloudFront                           | S3 Cross-Region Replication   |
|------------------------|--------------------------------------|--------------------------------|
| **Fonction principale**| Accélération livraison contenu mondial| Réplication régionale S3       |
| **Latence utilisateurs**| Très faible ✅                       | Réduite (mais plus élevée qu'un CDN) ⚠️ |
| **Stockage des données**| Cache temporaire (Edge Locations)    | Copie permanente des données ✅|
| **Disponibilité**      | Globale ✅                            | Régionale (multi-région) ✅    |
| **Coût**               | Bas à moyen (réduction transfert) ✅  | Plus élevé (double stockage) ⚠️ |
| **Gestion complexité** | Simple ✅                             | Moyenne à élevée ⚠️            |


- Utilise **CloudFront** pour servir très rapidement du contenu à des utilisateurs finaux partout dans le monde.
- Utilise **S3 Cross-Region Replication** pour la résilience, la disponibilité multi-région des données, ou des obligations légales.

---
## Decoupling applications: SQS, SNS, Kinesis, Active MQ
### Amazon Kinesis Data Firehose VS Kinesis Data Streams


| **Critère**                        | **Kinesis Data Streams**                      | **Kinesis Data Firehose**                            |
|-----------------------------------|----------------------------------------------|------------------------------------------------------|
| **Type de service**               | Data streaming (temps réel)                  | Ingestion & livraison automatisée (quasi temps réel)|
| **Niveau de gestion**             | Semi-géré : tu gères les shards & consommateurs | Full-managed (zéro infra à gérer)                   |
| **Latence**                       | ~200 ms                                      | 1 à 5 minutes environ                                |
| **Transformation des données**    | Personnalisée via app Lambda/EC2             | Intégrée via Lambda (facultatif)                    |
| **Stockage intermédiaire**        | Données retenues 24h à 7 jours (ou +)        | Pas de stockage réel (stream direct vers cible)     |
| **Destinations possibles**        | Traitement personnalisé                      | S3, Redshift, OpenSearch, Splunk, Datadog…          |
| **Cas d’usage typique**           | Monitoring temps réel, custom analytics      | Livrer des logs ou données brutes vers S3/Redshift  |

### SQS vs SNS vs Kinesis

| **Critère**                            | **Amazon SQS**                                                                 | **Amazon SNS**                                                           | **Amazon Kinesis (Data Streams)**                                              |
|----------------------------------------|--------------------------------------------------------------------------------|-------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| **Modèle d’architecture**              | File de messages (Queue)                                                      | Publication / abonnement (Pub/Sub)                                     | Streaming de données en temps réel                                            |
| **Mode de livraison**                  | Les consommateurs tirent les messages (**pull**)                              | SNS envoie activement les messages (**push**)                           | **Pull standard**, ou **Push** avec *Enhanced Fan-Out*                       |
| **Durée de vie des messages**          | Supprimés après consommation                                                  | Non persistés (perdus s’ils ne sont pas livrés)                         | Conservent les données **jusqu’à X jours** (24h à 365j)                       |
| **Ordonnancement**                     | Garanti uniquement avec **FIFO queues**                                       | Pas d’ordonnancement                                                    | Ordre garanti **au niveau du shard**                                         |
| **Scalabilité des consommateurs**      | Autant de consommateurs que nécessaire                                        | Jusqu’à **12,500,000** abonnés                                          | **1 MB/s en lecture / 2 MB/s en écriture par shard**<br>Enhanced Fan-Out : 2 MB/s par consommateur |
| **Débit à provisionner**               | ❌ Pas nécessaire                                                              | ❌ Pas nécessaire                                                        | ✅ **Oui**, sauf en mode on-demand                                           |
| **Fan-out (envoi à plusieurs cibles)** | Nécessite SNS ou un traitement manuel                                         | Intégré nativement                                                      | Possible via Enhanced Fan-Out                                                |
| **Cas d’usage typique**                | Découplage applicatif, traitement asynchrone                                  | Notifications multiples, diffusion simple                               | Traitement de flux en temps réel, analytics, ETL                             |
| **Fonctionnalités spéciales**          | Délai par message, DLQ, FIFO                                                  | Jusqu’à **100 000 sujets**                                              | Lecture en différé (replay), traitement Big Data                             |

---
## Containers on AWS

### Comparatif : ECS vs EKS vs Fargate

| **Critère**                         | **Amazon ECS**                             | **Amazon EKS**                             | **AWS Fargate**                                         |
|------------------------------------|--------------------------------------------|--------------------------------------------|---------------------------------------------------------|
| **Type de service**                | Orchestration de conteneurs AWS natif      | Orchestration de conteneurs via Kubernetes | Mode d'exécution (backend) pour ECS ou EKS              |
| **Orchestration**                  | Propriétaire AWS                           | Kubernetes (standard open-source)          | Géré par AWS                                            |
| **Compatibilité outils K8s**       | ❌ Non                                      | ✅ Oui (kubectl, Helm…)                    | ✅ Oui, via ECS ou EKS                                  |
| **Plan de contrôle**               | Géré par AWS                               | Géré par AWS                               | Pas concerné                                            |
| **Gestion des nœuds (compute)**    | EC2 ou Fargate                              | EC2, Fargate ou self-managed EC2           | **Zéro gestion EC2** (serverless)                       |
| **Courbe d’apprentissage**         | 📉 Facile                                   | 📈 Plus complexe (Kubernetes)              | 📉 Très simple                                          |
| **Flexibilité / Portabilité**      | ❌ Moins flexible, AWS-only                | ✅ Très flexible, multi-cloud possible     | ❌ AWS only, moins de personnalisation                  |
| **Scalabilité automatique**        | Oui (avec service + ALB)                    | Oui (avec HPA, Karpenter ou Fargate)       | Oui (auto-scaling intégré)                             |
| **Cas d’usage typique**            | Déploiements simples, applications web      | Architectures microservices avancées       | Conteneurs rapides, sans infra à gérer                  |
| **Facturation**                    | EC2 ou Fargate                              | EC2 ou Fargate                              | À la **seconde** (CPU + RAM utilisés)                  |

- **ECS** : simple, rapide, AWS-only, parfait pour déployer des apps conteneurisées sans trop de complexité.
- **EKS** : puissance et standardisation Kubernetes, mais plus technique.
- **Fargate** : **mode d’exécution serverless** pour ECS ou EKS → zéro infra à gérer.

---
## Databases

