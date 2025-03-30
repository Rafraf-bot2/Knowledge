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
## RDS + Aurora + ElastiCache
⚠️ **Read Replicas** peuvent etre setup en **Multi AZ for Disaster Recovery**

### RDS Backup
Si RDS DB stoppée pendant longtemps, prendre un snap et le restaurer (car RDS facture meme stoppée et snapshot coute moins cher).


| Critère            | RDS                      | Aurora                     | ElastiCache                 |
|--------------------|--------------------------|----------------------------|-----------------------------|
| Type               | Relationnel classique ✅ | Relationnel haute perf ✅  | Cache (clé-valeur) ✅       |
| Performance        | Bonne ⚠️                 | Excellente ✅              | Ultra-élevée (RAM) ✅       |
| Scalabilité        | Verticale ⚠️             | Horizontale (réplicas) ✅  | Horizontale (nœuds) ✅      |
| Persistance        | Élevée ✅                | Très élevée ✅             | Faible (en mémoire) ⚠️      |
| Latence            | Moyenne ⚠️               | Faible ✅                  | Ultra-faible ✅             |

- **RDS** : Base relationnelle traditionnelle, facile à gérer.
- **Aurora** : Performances supérieures, fiabilité maximale.
- **ElastiCache** : Accélère l’accès aux données en mémoire, ultra-rapide.

---
## Stockage

### S3

| Solution        | Analogie simple                                      | Utilisation typique      |
|-----------------|------------------------------------------------------|--------------------------|
| **S3**          | Grand coffre-fort en ligne                           | Stockage durable, partage|
| **EBS**         | Disque dur externe branché sur un ordinateur         | Stockage persistant      |
| **EFS**         | Disque réseau partagé par plusieurs ordinateurs      | Partage facile de fichiers|
| **Instance Store**| Clé USB très rapide, mais temporaire               | Données temporaires rapides|

- **S3** : Stockage objet, idéal pour fichiers statiques et archives.
- **EBS** : Stockage bloc performant et persistant pour EC2.
- **EFS** : Stockage fichier partagé entre instances EC2.
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

### Recap

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

