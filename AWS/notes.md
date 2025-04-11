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

Les Elastic Interfaces (ENI) sont limitées à une AZ spécifique.

---
## High availabity & Scalabilty
⚠️ GENEVE protocol port 6081 => Gateway Load Balancer

---
## Route 53

- **Service DNS (Domain Name System) managé par AWS**  
	  - Traduit les **noms de domaine** (ex. `example.com`) en **adresses IP**  
	  - Fournit aussi des fonctionnalités de **routing intelligent** et de **monitoring**

- **DNS haute disponibilité et faible latence**  
	  - Utilise une **infrastructure mondiale** redondée et répartie pour une **résolution rapide et fiable**  
	  - Haute tolérance aux pannes

- **Enregistrement de domaines**  
	  - Permet d’**acheter et gérer des noms de domaine** directement via AWS  
	  - Compatible avec des **TLD courants** (`.com`, `.org`, etc.)

- **Routage du trafic DNS**  

    | **Type de routage**           | **Description**                                                                 |
    |-------------------------------|----------------------------------------------------------------------------------|
    | **Simple**                    | Redirige vers une seule ressource (ex : 1 IP, 1 site)                            |
    | **Weighted**                  | Distribution selon des **poids définis** (ex : 70% vers A, 30% vers B)           |
    | **Latency-based**             | Envoie le client vers la **région AWS la plus rapide**                          |
    | **Failover**                  | Redirige vers une **cible de secours** en cas de panne de la principale         |
    | **Geolocation**               | Routage basé sur la **localisation géographique de l’utilisateur**              |
    | **Geoproximity (avec Traffic Flow)** | Routage par **proximité géographique**, pondérable                          |
    | **Multi-value Answer**        | Donne plusieurs réponses (façon round-robin), avec **vérification de santé**    |

- **Health checks intégrés**  
	  - Route53 peut **surveiller l’état de santé** de tes ressources (EC2, ELB, etc.)  
	  - Combine santé + type de routage (ex : failover automatique)

- **Intégration avec d’autres services AWS** :  
  - **ELB**, **CloudFront**, **S3 Static Website**, **API Gateway**, etc.  
  - Peut gérer des **alias records** (comme un CNAME mais sans coût DNS supplémentaire)

- **Sécurisation**  
  - Supporte **DNSSEC** (Domain Name System Security Extensions)  
  - Contrôle via **IAM policies** pour les zones hébergées

- **Pour l’examen AWS SAA**  
	- **Très fréquent** !  
		  - Bien maîtriser les **types de routage** et quand les utiliser  
		  - Savoir qu’un **alias record** peut pointer vers une ressource AWS sans coût supplémentaire  
		  - Comprendre le lien entre **health check**, **failover**, et **haute disponibilité**

---
## Golden AMI
Une **Golden AMI** (Amazon Machine Image "dorée") est une **image EC2 préconfigurée, sécurisée et standardisée**, utilisée comme **base unique pour lancer toutes les instances** d'une organisation.

### 🧠 En clair

- C’est une **AMI personnalisée** contenant :
  - Le **système d’exploitation**
  - Tous les **logiciels nécessaires** (ex : agent CloudWatch, outils internes, runtime applicatif)
  - Les **patchs de sécurité** à jour
  - Les **paramètres de configuration standards** (firewall, monitoring, users, etc.)

### 🎯 Pourquoi utiliser une Golden AMI ?

- **Standardisation** : toutes les instances lancées sont identiques et conformes aux règles de l’entreprise  
- **Sécurité** : tu maîtrises ce qu’il y a dans l’image (pas de dépendance sur une AMI publique inconnue)  
- **Gain de temps** : tu évites d’installer et configurer à chaque lancement  
- **Facilité d’audit et de conformité**  
- Tu peux l’utiliser dans un **pipeline CI/CD** pour automatiser les déploiements avec des images propres


---
## Elastic Beanstalk
- Plateforme **PaaS (Platform as a Service)** qui permet de **déployer automatiquement des applications web**  
- Tu envoies ton code, Beanstalk s’occupe de **toute l’infrastructure autour** :  
  - Provisionnement des ressources (EC2, Load Balancer, Auto Scaling, RDS, etc.)  
  - Déploiement de l’application  
  - Monitoring et mise à jour

- Compatible avec plusieurs environnements applicatifs :  
  - **Java, Python, .NET, PHP, Node.js, Ruby, Go, Docker**  
  - Prend en charge les **applications web** ou **les APIs backend**

- Tu gardes **le contrôle complet des ressources AWS sous-jacentes**  
  - Tu peux les personnaliser (EC2 type, ASG config, VPC, etc.)  
  - Beanstalk génère automatiquement une **stack CloudFormation**

- Intégré avec d'autres services AWS :  
  - **CloudWatch** pour les logs/metrics  
  - **IAM** pour contrôler les permissions  
  - **RDS** si besoin de base de données

### Avantages

- Déploiement **rapide** d’une appli en quelques clics ou lignes de commande  
- **Pas besoin de gérer manuellement les ressources** AWS  
- Très utile pour les **environnements de test, staging, ou dev rapide**  
- Supporte **rolling updates**, **blue/green deployments**, **monitoring**, **auto scaling**

### Limitations

- Moins flexible qu’une architecture totalement managée à la main  
- Moins adapté aux **architectures microservices complexes**  
- **Opinionated** : AWS prend certaines décisions pour toi


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
- **Lambda@Edge** : Fonctionnalité d’AWS Lambda qui permet d’exécuter du code à proximité de l’utilisateur final, directement dans les Edge Locations de CloudFront
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


