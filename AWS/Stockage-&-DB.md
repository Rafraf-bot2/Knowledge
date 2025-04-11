# Stockage & DB
## 🗃️ **Comparatif : S3 + Principales Bases de Données AWS**

| **Service**           | **Type / Modèle**                                               | **Cas d’usage principal**                                              | **Key Features**                                                                                                                                                                                       |
|-----------------------|-----------------------------------------------------------------|------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Amazon S3**         | **Object Storage**                                              | Stockage et sauvegarde de fichiers volumineux, sites statiques, backups, distribution de contenu | - Scalabilité et durabilité élevées<br>- Jusqu’à 5 To par objet<br>- Multiples classes de stockage (Standard, IA, Glacier...)<br>- Versioning, Lifecycle Policies, Event Notifications (SQS, SNS, Lambda)<br>- Sécurité (IAM, Bucket Policies, SSE, MFA-Delete) |
| **Amazon RDS**        | **Relationnelle managée**<br>(MySQL, PostgreSQL, Oracle, SQL Server, MariaDB, RDS Custom) | Applications web classiques, OLTP, bases SQL existantes                  | - Multi-AZ, Read Replicas<br>- Sauvegardes automatiques et snapshots<br>- IAM, Secrets Manager intégrés<br>- Maintenance, patching gérés par AWS                                                                                              |
| **Amazon Aurora**     | **Relationnelle haute performance**<br>(Compatible MySQL / PostgreSQL) | Applis critiques nécessitant haute dispo et performances supérieures   | - Jusqu’à 5x + rapide que MySQL, 3x + rapide que PostgreSQL<br>- Stockage distribué (6 copies sur 3 AZ)<br>- Auto-scaling (Aurora Serverless v2)<br>- Jusqu’à 15 Read Replicas, Global Database multi-région                                     |
| **ElastiCache**       | **Cache en mémoire**<br>(Redis ou Memcached)                    | Améliorer la performance en soulageant la base de données (cache sessions, leaderboards…) | - Latence ultra-faible (données en RAM)<br>- Auto-scaling, snapshots (Redis)<br>- Réplication, haute dispo<br>- Géré par AWS (maintenance, monitoring)                                                                                         |
| **Amazon DocumentDB** | **NoSQL orienté document**<br>(MongoDB-compatible)             | Applications utilisant l’API MongoDB / JSON, migrations depuis MongoDB  | - Haute dispo (6 copies / 3 AZ)<br>- Stockage auto-extensible (jusqu’à 64 To)<br>- Maintenance automatisée<br>- Intégration IAM, KMS, VPC                                                                                                      |
| **Amazon Neptune**    | **Base de données de graphes**                                 | Social networks, moteurs de recommandation, graphes de connaissances    | - Support Gremlin & SPARQL<br>- Haute dispo (6 copies / 3 AZ), jusqu’à 15 réplicas<br>- Gère des milliards de relations<br>- Faible latence (ms)                                                                                               |
| **Amazon Keyspaces**  | **NoSQL columnaire**<br>(Cassandra-compatible)                 | Migrations Cassandra, IoT, time-series, logs                             | - Scalabilité on-demand ou provisionnée<br>- Compatibilité CQL<br>- Latence ms, milliers de req/s<br>- Sécurité (IAM, KMS, PITR)                                                                                                              |
| **Amazon Timestream** | **Base de données time series**                                | IoT, métriques, logs, analytics en (quasi) temps réel                   | - Serverless & auto-scaling<br>- Tiers de stockage (in-memory / historique)<br>- SQL spécialisé time-series<br>- Sécurité (KMS, IAM), gestion trillions d’événements                                                                          |
| **Amazon DynamoDB**   | **NoSQL clé-valeur**                                           | Haute performance, latence ms, e-commerce, gaming, serverless           | - Serverless, scalable quasi à l’infini<br>- Global Tables (multi-région)<br>- DynamoDB Streams + Lambda<br>- Point-in-time Recovery (35 jours)<br>- Sécurité (chiffrement, IAM, VPC endpoints)                                              |

1. **S3** : Stockage d’objets (fichiers volumineux, distribution de contenu).  
2. **RDS / Aurora** : Bases relationnelles (SQL).  
3. **ElastiCache** : Cache en mémoire (Redis/Memcached).  
4. **DocumentDB** : Base orientée documents (MongoDB).  
5. **Neptune** : Base orientée graphes.  
6. **Keyspaces** : Cassandra managée (columnaire).  
7. **Timestream** : Time-series data (IoT, monitoring).  
8. **DynamoDB** : NoSQL clé-valeur performante et scalable.


## Amazon RDS

**Amazon RDS (Relational Database Service)** permet d’exécuter facilement des bases de données relationnelles sans avoir à gérer l’infrastructure (serveurs, patching, sauvegardes...).


| Élément                                     | Description                                                                                  |
|--------------------------------------------|----------------------------------------------------------------------------------------------|
| **Engines supportés**                      | PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, **RDS Custom** (Oracle/SQL Server avancé)   |
| **Dimensionnement**                        | Taille de l’instance et type/volume de stockage **EBS** configurables                       |
| **Auto-scaling du stockage**               | ✅ Le stockage peut **s’ajuster automatiquement** à la croissance des données                |
| **Haute disponibilité**                    | - **Multi-AZ** : réplique synchronisée pour tolérance aux pannes 🔁<br>- **Read Replicas** : pour répliquer en lecture seule et scaler les requêtes (15 max) |
| **Sécurité**                               | - Intégration IAM (authentification)<br>- Chiffrement avec **KMS** (au repos)<br>- **SSL** (en transit)<br>- Groupes de sécurité |
| **Sauvegardes automatiques**               | ✅ Restauration **point-in-time** jusqu’à 35 jours                                           |
| **Snapshots manuels**                      | ✅ Sauvegardes persistantes pour restaurations à long terme                                  |
| **Maintenance gérée**                      | AWS applique les **patchs** et met à jour les moteurs selon un **planning configurable**     |
| **Secrets Manager**                        | Intégration native pour gérer **les identifiants** de connexion de manière sécurisée        |
| **RDS Custom**                             | Permet d’accéder au système d’exploitation (cas Oracle & SQL Server) pour des besoins spécifiques |
| **Cas d’usage typiques**                   | - Applications OLTP<br>- Données relationnelles<br>- Requêtes SQL<br>- Transactions multi-tables |

### RDS Proxy

| **Aspect**                  | **Amazon RDS Proxy**                                                                 |
|----------------------------|----------------------------------------------------------------------------------------|
| **Type de service**        | Service managé de **proxy de base de données**                                        |
| **Rôles principaux**       | **Pooling de connexions**, **failover rapide**, **sécurité renforcée**                |
| **Bénéfices**              | - Moins de connexions ouvertes directement sur RDS<br>- Réduction du temps de failover<br>- Optimisation des performances applicatives |
| **Bases supportées**       | RDS MySQL, RDS PostgreSQL, Aurora MySQL, Aurora PostgreSQL                           |
| **Compatible Lambda ?**    | ✅ Oui, recommandé pour les fonctions Lambda connectées à RDS                         |
| **Sécurité**               | - Authentification IAM<br>- Chiffrement TLS<br>- Intégration avec **AWS Secrets Manager** |
| **Gestion des connexions** | Oui (pooling et réutilisation automatique)                                            |
| **Utilisation typique**    | - Architectures **serverless** ou à **fort trafic court**<br>- Besoin de **basculement rapide** |
| **Limites**                | - Pas encore compatible avec tous les moteurs RDS<br>- **Ajoute une couche intermédiaire** |

## Amazon Aurora

**Amazon Aurora** est une base de données **relationnelle managée**, compatible **PostgreSQL** et **MySQL**, conçue par AWS pour offrir les **performances des bases propriétaires** (comme Oracle) tout en conservant la **simplicité et le coût des bases open-source**.

| Élément                                       | Description                                                                                      |
|----------------------------------------------|--------------------------------------------------------------------------------------------------|
| **Engines supportés**                        | ✅ **Aurora MySQL**<br>✅ **Aurora PostgreSQL**                                                  |
| **Performance & scalabilité**                | Jusqu’à **5 fois plus rapide que MySQL**, **3 fois plus rapide que PostgreSQL**<br>Supporte jusqu'à **15 Read Replicas** avec <1 sec de latence |
| **Stockage distribué et auto-scalable**      | Jusqu’à **128 To** sans intervention manuelle, stockage partagé entre les instances             |
| **Haute disponibilité intégrée**             | Multi-AZ par défaut avec réplication synchronisée sur **6 copies réparties sur 3 AZs**           |
| **Failover automatique**                     | En cas de panne, Aurora bascule automatiquement sur une instance de secours                      |
| **Backups automatiques**                     | Restauration **point-in-time** sur 35 jours + **snapshots manuels** disponibles                 |
| **Aurora Serverless v2**                     | Mode **on-demand** : auto-scale selon la charge, facturation à la seconde, idéal pour usage intermittent |
| **Sécurité**                                 | Chiffrement **KMS** (au repos), **SSL/TLS** (en transit), IAM Auth + Secrets Manager             |
| **Migration simplifiée**                     | Compatible avec les outils MySQL/PostgreSQL pour les migrations                                 |
| **Global Database**                          | Réplication multi-région avec <1s de latence lecture, idéal pour les apps globales              |
| **Cas d’usage typiques**                     | - Applis critiques (OLTP)<br>- Haute dispo requise<br>- Applis mondiales<br>- Microservices SQL |


## Amazon S3
**Amazon S3**, c’est un gigantesque **“coffre-fort”** pour **stocker des fichiers (objets)** dans le cloud.  
Tu peux y mettre des images, vidéos, backups, etc., jusqu’à **5 To par objet**. S3 est **hautement disponible et scalable**, et tu paies uniquement pour l’espace réellement occupé.

> L’avantage : c’est **serverless**, évolutif à l’infini, et tu peux utiliser différentes **classes de stockage** (Standard, IA, Glacier…) pour **optimiser tes coûts**.

- **Clé/valeur** : chaque fichier (objet) est identifié par une clé unique dans un bucket.  
- Pas idéal pour **des millions de tout petits fichiers** (latence proportionnelle à la quantité d’objets).

| Élément                               | Description                                                                                   |
|---------------------------------------|-----------------------------------------------------------------------------------------------|
| **Taille max. par objet**             | 5 To                                                                                          |
| **Versioning**                         | Conserve des copies de différentes versions d’un même objet                                  |
| **Classes de stockage (Tiers)**       | Standard, Infrequent Access (IA), Intelligent-Tiering, Glacier (archivage), etc.              |
| **Cycle de vie (Lifecycle)**          | Passage automatique d’une classe de stockage à une autre selon l’âge de l’objet              |
| **Sécurité**                           | IAM, Bucket Policies, ACL, S3 Access Points, **MFA Delete**, chiffrement SSE-S3 / SSE-KMS     |
| **Logs & Access**                     | Bucket Access Logging, Object Lock, WORM (Write-Once-Read-Many), Vault Lock                  |
| **Performance**                       | Multi-part upload, **S3 Transfer Acceleration**, S3 Select                                   |
| **Batch operations**                  | **S3 Batch** pour des modifications massives (tags, ACL…)                                    |
| **Notifications**                     | **S3 Event Notifications** (via SNS, SQS, Lambda, EventBridge)                               |
| **Cas d’usage**                       | Fichiers statiques (sites), sauvegardes, key-value store de gros objets, hébergement web     |

### S3 / EBS / EFS / Instance Store

| Solution        | Analogie simple                                      | Utilisation typique      |
|-----------------|------------------------------------------------------|--------------------------|
| **S3**          | Grand coffre-fort en ligne                           | Stockage durable, partage|
| **EBS**         | Disque dur externe branché sur un ordinateur         | Stockage persistant      |
| **EFS**         | Disque réseau partagé par plusieurs ordinateurs      | Partage facile de fichiers|
| **Instance Store**| Clé USB très rapide, mais temporaire               | Données temporaires rapides|

- **S3** : Stockage objet, idéal pour fichiers statiques et archives.
- **EBS** : Stockage bloc performant et persistant pour EC2 (Lock dans sa AZ).
- **EFS** : Stockage fichier partagé entre instances EC2 (Plusieurs AZ).
- **Instance Store** : Stockage ultra-rapide mais temporaire.

### S3 : Sécurité


| Méthode         | Gestion des clés    | Facilité            | Contrôle clés        | Niveau sécurité |
|-----------------|---------------------|---------------------|----------------------|-----------------|
| **SSE-S3**      | AWS                 | ✅ Très facile       | ⚠️ Faible            | Élevé           |
| **SSE-KMS**     | AWS KMS             | ✅ Facile à moyen    | ✅ Élevé             | Très élevé      |
| **SSE-C**       | Client              | ⚠️ Complexe         | ✅ Très élevé        | Très élevé      |
| **Client-Side** | Client ou AWS KMS   | ⚠️ Complexe         | ✅ Très élevé        | Très élevé      |

- **SSE-S3** : simplicité maximale.
- **SSE-KMS** : équilibre simplicité/contrôle.
- **SSE-C / Client-side** : contrôle total, mais gestion complexe.


- CORS (**Cross-Origin Resource Sharing**) permet à un **navigateur** d’accéder à des ressources situées sur un **domaine différent** de celui de l'application web  
- Par défaut, **S3 bloque les requêtes cross-origin** pour des raisons de sécurité

- Si ton **frontend (JavaScript dans le navigateur)** est hébergé sur `siteA.com` et qu’il tente d’accéder à un fichier stocké sur un **bucket S3 de `siteB.com`**, ça sera **bloqué** sans configuration CORS  
- Pour autoriser cette interaction, il faut **ajouter une CORS configuration** dans les **paramètres du bucket S3**

- Utilisé pour des cas comme :
  - Chargement d’images ou de fichiers depuis S3 via un site web  
  - Intégration d’un frontend hébergé ailleurs (ex : Netlify) qui interagit avec un backend S3

- Pour l’examen AWS SAA, savoir que :
  - Sans CORS, **les navigateurs bloquent les appels cross-origin vers S3**
  - **La config CORS est définie au niveau du bucket**
  - Elle est **nécessaire pour les applications web frontend** qui accèdent à S3 depuis le navigateur


### Glacier

| Critères            | Glacier Instant Retrieval  | Glacier Flexible Retrieval | Glacier Deep Archive  |
|---------------------|----------------------------|----------------------------|-----------------------|
| Temps récupération  | Millisecondes ✅           | Minutes à quelques heures ⚠️| Jusqu’à 12 heures ⚠️  |
| Coût                | Moyen                      | Très bas ✅                 | Extrêmement bas ✅     |
| Usage typique       | Archives nécessitant accès rapide| Archives fréquentes, mais pas urgentes|Archives très long terme|

- Utilise **Glacier** quand tu veux économiser fortement sur le coût de stockage des données que tu n’utilises que rarement.
- Choisis la classe selon la rapidité dont tu as besoin pour récupérer tes données.

### FSx for Luster

| Critère                   | FSx for Lustre                          |
|---------------------------|-----------------------------------------|
| Type de système de fichier| Parallèle haute performance (Lustre)    |
| Performance               | Très élevée (débit + latence) ✅         |
| Accès simultané           | Multi-EC2 ✅                             |
| Intégration S3            | Oui, native (import/export automatique) ✅ |
| Cas d’usage               | HPC, IA/ML, Big Data, analyse rapide    |


Choisis **FSx for Lustre** si tu as besoin d’un système de fichiers **ultra rapide et partagé** pour traiter **de gros volumes de données à la volée**, comme dans les projets d’analyse, de simulation ou d’IA.

### AWS Storage Gateway

| Mode Gateway      | Protocole       | Stockage AWS cible | Cas d’usage principal                    |
|-------------------|-----------------|--------------------|------------------------------------------|
| File Gateway      | NFS / SMB       | Amazon S3          | Partage de fichiers, archivage cloud     |
| Volume Gateway    | iSCSI           | Amazon S3 / EBS    | Extension de stockage local, sauvegardes |
| Tape Gateway      | VTL (iSCSI)     | S3 + Glacier        | Remplacement des bandes de sauvegarde    |


**AWS Storage Gateway** est la solution si tu veux connecter ton environnement local au cloud **sans tout changer**, tout en profitant des avantages de **S3, Glacier et EBS** pour la sauvegarde, l’archivage et l’extension de capacité.

## DynamoDB
- Base de données **NoSQL** managée, hautement disponible, scalable, serverless  
- Stocke les données sous forme de **tables**, chaque entrée est un **item**, avec une **clé primaire** (partition key, optionnellement sort key)  
- Idéale pour des applications à haute vitesse : IoT, jeux, mobile, e-commerce, logs

- Mode de capacité :
    - **Provisioned** :
        - Tu définis combien de **Read Capacity Units (RCU)** et **Write Capacity Units (WCU)** tu veux réserver
        - 1 **RCU** = 1 lecture cohérente forte/seconde pour un objet de 4 Ko (ou 2 lectures éventuelles)
        - 1 **WCU** = 1 écriture/seconde pour un objet de 1 Ko
    - **On-Demand** :
        - Tu ne gères pas la capacité, tu paies à l’usage (lectures/écritures réelles)
        - Pratique pour charges imprévisibles ou début de projet

- Deux types de lectures :
    - **Strongly consistent** : données toujours à jour (1 RCU = 1 lecture 4 Ko)
    - **Eventually consistent** : plus rapides et moins chères (1 RCU = 2 lectures 4 Ko)

- Clés primaires :
    - **Partition key uniquement** : chaque item a une clé unique
    - **Partition key + Sort key** : permet plusieurs entrées avec même partition key, triées par sort key

- Les données sont distribuées automatiquement sur plusieurs partitions selon la **partition key**  
    - Il faut **bien choisir la clé** pour **éviter les "hot partitions"** (trop de requêtes concentrées)

- Index :
    - **GSI (Global Secondary Index)** : autre combinaison clé/tri, interrogeable à tout moment
    - **LSI (Local Secondary Index)** : même partition key, autre sort key

- Accès par API uniquement (pas de SQL), via **SDK AWS ou CLI**

- **DynamoDB Accelerator (DAX)** :
    - **Cache en mémoire** entièrement managé pour DynamoDB
    - Réduit la latence de lecture à **quelques millisecondes** ou moins
    - Fonctionne comme un **proxy en lecture seule**, compatible avec les SDK AWS
    - À activer dans les cas où tu as beaucoup de **read throughput élevé et répétitif**

- **Transactions** :
    - Supporte les transactions **ACID** pour des écritures atomiques multi-items ou multi-tables

- **TTL (Time to Live)** :
    - Tu peux définir une **date d’expiration automatique** pour chaque item (colonne TTL)

- **Streams** :
    - Chaque modification de table peut être capturée dans un flux DynamoDB Stream
    - Intégrable avec **Lambda** pour déclencher des actions en cas de modification

- **Sauvegardes** :
    - **On-Demand Backup** : snapshots manuels
    - **Point-In-Time Recovery (PITR)** : restauration à n’importe quel instant sur les 35 derniers jours

- **Auto Scaling** :
    - En mode provisioned, DynamoDB peut **ajuster automatiquement les RCU/WCU** selon la charge

- **DynamoDB Global Tables** :
    - Réplication multi-région active/active automatique  
    - Pour applications **globales** avec **latence très faible** et **haute disponibilité**

- Pour l’examen AWS SAA :
  - Notion **très fréquente** dans les questions d’architecture, performance, coût, caching
  - Bien différencier :
    - **On-Demand vs Provisioned**
    - **GSI vs LSI**
    - **RCU/WCU** (et leurs unités de mesure)
    - **DAX** (cache lecture, faible latence, lecture uniquement)
    - **Streams** (intégration Lambda)
    - **Global Tables** (multi-région, active/active)


## Recap

| **Catégorie**                  | **Service**                    | **Description**                                                                                  |
|-------------------------------|--------------------------------|--------------------------------------------------------------------------------------------------|
| 🪣 **Stockage Objet**          | **S3**                         | Stockage d’objets scalable (images, vidéos, backups, etc.).                                      |
|                               | **S3 Glacier**                | Archivage à long terme à bas coût, accès lent.                                                  |
| 💽 **Stockage Bloc**           | **EBS**                        | Disque réseau pour **1 instance EC2** à la fois, persistant.                                    |
|                               | **Instance Store**             | Stockage **local physique** pour EC2, très rapide mais éphémère.                                |
| 📁 **Stockage Fichier**        | **EFS**                        | Système de fichiers réseau **Linux** (POSIX), partagé entre plusieurs instances.                |
|                               | **FSx for Windows**            | Système de fichiers **Windows**, compatible SMB, Active Directory.                              |
|                               | **FSx for Lustre**             | Système haute performance pour le **HPC** (calcul intensif).                                     |
|                               | **FSx for NetApp ONTAP**       | Système de fichiers riche, **compatibilité OS étendue** (SMB, NFS, iSCSI).                      |
|                               | **FSx for OpenZFS**            | Système **ZFS managé**, avec snapshots, compression, etc.                                       |
| 🔄 **Hybride & Transfert**     | **Storage Gateway**            | Passerelle entre local et AWS (File, Volume, Tape Gateway).                                     |
|                               | **Transfer Family**            | Serveur **FTP, FTPS, SFTP** vers S3 ou EFS.                                                     |
|                               | **DataSync**                   | Synchronisation planifiée entre **on-premises ↔ AWS** ou entre services AWS.                   |
|                               | **Snowcone / Snowball / Snowmobile** | Transfert **physique** de gros volumes de données vers AWS.                          |
| 🧠 **Base de données**         | **Amazon RDS / Aurora / DynamoDB** | Stockage spécialisé avec **indexation & requêtes** intégrées.                           |

### DocumentDB
- **Base NoSQL** orientée documents (JSON).
- Conçu pour les applications **qui utilisent l’API MongoDB** (versions 3.6, 4.0, 4.2…).
- **Haute disponibilité** et **réplication multi-AZ**, similaire à Aurora (6 copies de données réparties sur 3 zones de dispo).
- **Stockage auto-extensible** : démarre à 10 Go et peut grandir par incréments de 10 Go, jusqu’à 64 To.
- Gère **des charges massives** (plusieurs millions de requêtes par seconde) en lecture et en écriture.
- Automatisation de la maintenance, des patchs et des backups.
- Sécurité : Intégration **VPC**, **IAM**, chiffrement au repos avec **KMS**, SSL/TLS en transit.
  
#### Comparaison conceptuelle
- **DocumentDB** ↔ **MongoDB**  
- **Aurora** ↔ **MySQL / PostgreSQL**  

Dans les deux cas, AWS propose une version managée, plus simple à opérer, avec une haute disponibilité et une grande scalabilité native.

### Amazon Neptune
**Amazon Neptune** est une **base de données orientée graphes**.  
Plutôt que de stocker tes données sous forme de tables (SQL) ou de documents (NoSQL), tu les représentes sous forme de **nœuds** et de **relations**.  
Imagine un **réseau social** : tu as des utilisateurs (nœuds), qui ont des relations d’amitié ou qui aiment des posts (relations). Neptune est **optimisé** pour ce type de données fortement connectées.

- **Base de données de graphes managée** par AWS.
- Prend en charge les **API** Gremlin (TinkerPop) et SPARQL (RDF) pour requêter les graphes.
- **Haute disponibilité** : réplication sur 3 AZ, jusqu’à 15 réplicas de lecture.
- **Évolutivité** : capable de gérer **des milliards de relations** avec une **latence de l’ordre de la milliseconde**.
- Sécurité : **VPC**, **KMS** (chiffrement au repos), **IAM** pour le contrôle d’accès, **SSL** en transit.
- Réplications automatiques, backups, maintenance gérée.
- **Cas d’usage** :
	- **Réseaux sociaux** (amis, likes, commentaires…)
	- **Moteurs de recommandation** (produits liés, suggestions personnalisées)
	- **Détection de fraude** (analyse de connexions complexes entre entités)
	- **Graphes de connaissances** (ex: Wikipedia, management de données sémantiques)

### Amazon Keyspaces (for Apache Cassandra)
C’est comme avoir **Apache Cassandra** (une base NoSQL distribuée) mais **en mode managé** par AWS.  
Tu n’as plus à te soucier des serveurs, du déploiement, ni de la complexité Cassandra : **AWS gère tout**.  
Tu peux **créer et requêter tes tables** via le langage **CQL** (Cassandra Query Language), et l’infrastructure s’adapte automatiquement à la montée en charge.

- **Compatibilité** : utilise le langage **CQL** et le protocole Cassandra 3.11.
- **Haute disponibilité** : les données sont répliquées **3 fois** sur plusieurs zones de disponibilité.
- **Scalabilité automatique** :  
  - **On-demand** : AWS gère la capacité en temps réel.  
  - **Provisioned** : tu définis un débit, avec auto-scaling possible.
- **Performances** : latence en **millisecondes** à n’importe quelle échelle, prend en charge **des milliers de requêtes par seconde**.
- **Sécurité** : prise en charge de la **sécurité en transit (TLS)**, chiffrement au repos (AWS KMS), intégration IAM, backups et **Point-In-Time Recovery** (jusqu’à 35 jours).
- **Cas d’usage** :  
	- Stocker des données massives d’IoT (capteurs…)  
	- Time-series data (log events, clics…)  
	- Applications requérant des lectures/écritures à grande échelle.
