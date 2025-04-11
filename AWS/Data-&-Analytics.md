# Data & Analytics
## Amazon Athena 

**Amazon Athena**, c’est comme un **moteur de requêtes SQL** directement sur tes données stockées dans **S3**.  

- **Service serverless** : aucun cluster ou serveur à gérer.
- **Repose sur Presto** (moteur SQL distribué), plus communément appelé **Trino** aujourd’hui.
- **Intégration native S3** : tu peux **analyser** les données dans divers formats (CSV, JSON, ORC, Parquet, AVRO) stockés dans **n’importe quel bucket**.
- **Facturation** : basée sur le **volume de données scannées** (optimise donc en compressant / partitionnant / convertissant en Parquet).
- **Partage de métadonnées** : utilise le **Glue Data Catalog** (ou un Hive Metastore) pour stocker la structure des tables (schéma).
- **Performance** : meilleure si tes données sont **partitionnées** (par date, par catégorie) et dans un **format colonne** (ex. Parquet).

- **Cas d’usage** : 
	- Analyses ad hoc de logs (CloudTrail, ELB logs, etc.) directement dans S3.  
	- **Data lake** : extraire/combiner des données sans ETL lourd.  
	- Reporting ponctuel, exploration rapide des données.

## Redshift

**Amazon Redshift**, c’est un **data warehouse** (entrepôt de données) managé.  
Contrairement à une base transactionnelle (genre RDS), Redshift est **optimisé pour le traitement analytique** de **grandes quantités de données** (généralement historiques), où tu vas faire des **requêtes complexes** (agrégations, jointures lourdes).  
Il stocke les données **en colonnes** plutôt qu’en lignes, ce qui facilite les **analyses massives** et réduit l’espace disque.

> En clair, **Redshift** te permet de **faire du BI, des dashboards** et des **rapports** sur de gros volumes de données, avec des **performances nettement supérieures** à une base SQL classique pour la partie analytique.

- **Stockage en colonnes** : idéal pour les requêtes OLAP (analyses).
- **Clusters** : un nœud leader + nœuds compute qui parallélisent les requêtes.
- **Types de nœuds** :  
  - **RA3** (scalable, stockage sur S3 avec caching local),  
  - **DC2** (SSD local),  
  - **DS2** (HDD local) (plus ancien).
- **Sécurité** : VPC, chiffrement KMS, IAM, etc.
- **Intégration S3** : via **Redshift Spectrum**, tu peux requêter directement des données dans S3 **sans les charger** dans Redshift.
- **Concurrence** : supporte de multiples utilisateurs exécutant des requêtes simultanément.
- **Maintenance & backups** : automatisées par AWS, snapshots, point-in-time restore.

- **Cas d’usage** :
	- **Entreposage de données** (Data Warehouse) pour analyses BI (Business Intelligence).
	- Grandes tables pour calculer des agrégats, effectuer des jointures lourdes.
	- Génération de rapports, dashboards (avec QuickSight, Tableau, Power BI…).

## Opensearch

Voici une présentation **en deux parties** d’**Amazon OpenSearch Service** (anciennement **Amazon Elasticsearch Service**), avec un lien vers l’examen **AWS SAA** :


**Amazon OpenSearch Service**, c’est comme un **moteur de recherche** managé qui te permet de faire des **recherches ultra-rapides** sur de gros volumes de données textuelles ou semi-structurées.  
Tu peux y stocker des logs, des documents (JSON), et exécuter des **requêtes de recherche** ou **d’analytique** (agrégations) en **temps quasi réel**.  
C’est super pour les **dashboards** de monitoring, la recherche libre-texte, ou l’analyse de logs.

> En clair, **OpenSearch** est à la fois une **base orientée recherche** et un **moteur d’analyse** pour gros volumes de données, sans config manuelle d’Elasticsearch.

- **Compatible avec les APIs Elasticsearch / OpenSearch** → tu peux utiliser les **outils Kibana / OpenSearch Dashboards** pour visualiser tes données.
- **Gestion managée** : pas besoin d’installer ou de patcher Elasticsearch, AWS s’en charge.  
- **Scalabilité** : tu peux choisir le nombre de nœuds, leur type (général / RAM / storage-optimized), la taille du cluster, et configurer l’auto-scaling (certaines limites).
- **Sécurité** : Intégration IAM, politiques de contrôle d’accès, VPC, chiffrement (KMS), etc.
- **Domaines** : tu crées un “domaine” OpenSearch qui contient tes index (collections de documents).
- **Use cases** :  
  - Recherche de texte en **plein texte** (web, e-commerce)  
  - **Analyse de logs** (avec ingestion via Kinesis Firehose, Logstash, ou Beats)  
  - Monitoring de performances (métriques, dashboards)  
  - Visualisations interactives (Kibana / OpenSearch Dashboards)


- **Cas d’usage typique** :
	- **Recherche** (catalogues, sites web, documents textuels)
	- **Analytics en temps réel** sur des **logs** (observabilité, SIEM)
	- **Dashboarding** (Kibana / OpenSearch Dashboards)

## EMR

**Amazon EMR**, c’est un service **managé** qui te permet de déployer facilement des **clusters Big Data** basés sur des **outils open-source** comme Hadoop, Spark, Hive, Presto, etc.  
Au lieu d’installer et de configurer toi-même un **cluster Hadoop** (avec toute sa complexité), AWS le fait pour toi ; tu as juste à choisir la taille du cluster (nombre de nœuds EC2) et le logiciel que tu veux utiliser.

> **En clair**, tu peux **analyser de gigantesques volumes de données** ou **exécuter des jobs big data** (batch, analytics) sans gérer toute l’infrastructure.  


## ⚙️ **Amazon EMR (Elastic MapReduce)** :
- **Basé sur EC2** : tu as un **cluster** (master + core + task nodes) géré par EMR.
- **Intégration** :  
  - **S3** (stockage principal des données)  
  - **EMRFS** (interface S3 compatible HDFS)  
  - **AWS Glue Data Catalog** pour la découverte de schémas  
  - **CloudWatch** pour logs et monitoring
- **Logiciels pris en charge** : Hadoop, Spark, Hive, Presto, HBase, Pig, etc.
- **Tarification** : au temps de fonctionnement du cluster et selon la taille des instances.
- **Architecture** : cluster ephemeral (tu le lances pour un job, puis tu l’arrêtes) ou permanent (toujours actif).
- **Sécurité** : IAM (rôles), chiffrement KMS, isolation VPC, etc.

- **Cas d’usage** :
	- Analyses de gros volumes (multi-To / Po) en batch (ETL, machine learning Spark, etc.).
	- Traitement Hadoop/Spark pour générer des données agrégées, transformer des datasets avant de les stocker en data warehouse (Redshift) ou en data lake (S3).

## Quicksight
Amazon QuickSight est un service **BI (Business Intelligence) entièrement managé** qui permet de créer rapidement des **tableaux de bord interactifs** et des **visualisations de données**. Il est **serverless**, ne nécessite pas d'infrastructure à gérer, et s’intègre avec de nombreuses sources de données (S3, RDS, Redshift, Athena, DynamoDB, etc.). Voici ses points essentiels :

- **Principales caractéristiques** :
	  - **Analyses interactives et tableaux de bord** : permet de concevoir des graphiques et rapports dynamiques, directement partageables avec les utilisateurs.  
	  - **Pay-per-session** : facturation flexible selon le nombre de sessions (ou licences mensuelles selon l’offre).  
	  - **ML Insights** : fonctionnalités d'analyse augmentée (détection d’anomalies, prévisions automatisées).  
	  - **Sécurité et gestion des accès** : intégration IAM, chiffrement, row-level security pour filtrer la donnée au niveau utilisateur.  
	  - **Mode SPICE** : moteur in-memory pour accélérer les analyses et réduire les coûts des requêtes directes.

- **Cas d’usage** :  
	  - **Rapports et tableaux de bord pour la direction** : visualisation rapide des KPI (ventes, trafic, coûts).  
	  - **Analyses ad hoc** : exploration ponctuelle de données S3, Redshift, RDS, etc.  
	  - **Self-service BI** : chaque équipe peut créer ses propres visualisations.  
	  - **Partage rapide** : publication de dashboards à de nombreux utilisateurs, avec ou sans compte AWS.
- **Pour l’examen AWS SAA** :
	- Peut apparaître dans les questions liées à l’analytique ou au reporting.
	- Différenciation par rapport à d’autres services analytiques (Athena, Redshift, EMR) : QuickSight est purement visualisation / dashboard, pas un moteur de calcul Big Data.

## AWS Glue
AWS Glue est un service d’intégration de données entièrement managé qui facilite la découverte automatique des schémas, la préparation et la transformation des données. Il est souvent utilisé pour construire et gérer des pipelines de données vers des data lakes et des data warehouses (ex : Redshift). Il repose notamment sur des jobs ETL (Spark Python / Scala) et un catalogue central (AWS Glue Data Catalog) pour documenter les métadonnées.

- **Principales caractéristiques** :  
  - **Data Catalog** : crée automatiquement une structure (base de données, tables) en scannant S3 et d’autres sources.  
  - **ETL managé (Spark)** : exécute des jobs de transformation (Python ou Scala), sans gérer de cluster.  
  - **Crawler automatique** : détecte les schémas et formate les métadonnées dans le Glue Data Catalog.  
  - **Triggers et Workflows** : orchestre des tâches ETL, planifiées ou déclenchées par un événement.  
  - **Intégration étroite** : fonctionne avec S3, Redshift, RDS, DynamoDB, Athena, EMR, etc.  

- **Cas d’usage** :  
  - **Migration et nettoyage de données** : extraire, transformer et charger en data warehouse (ex : Redshift).  
  - **Data lake** : inventaire des tables (schemas) dans S3, recherche de données via Glue Data Catalog.  
  - **ETL serverless** : pas besoin de créer un cluster Spark manuel.  
  - **Orchestration** : automatisation de pipelines Big Data (ingestion → transformation → chargement).  
- **Pour l’examen AWS SAA** :
	- Peut apparaître dans les questions d’architecture Big Data.
	- Se compare à EMR + scripts custom ou à Lambda pour des transformations simples. Glue est pratique pour du ETL managé et le catalogage de données.

## AWS Lake Formation
AWS Lake Formation est un service conçu pour automatiser la création et la sécurisation d’un data lake sur AWS. Il simplifie l’ingestion, le catalogage et la configuration de politiques de sécurité centralisées pour toutes les données stockées (souvent dans S3). Il s’appuie sur le Glue Data Catalog et offre des fonctionnalités avancées pour contrôler l’accès aux données (niveau table, colonne, voire cellule).

- **Principales caractéristiques** :  
	  - Simplifie et accélère la mise en place d’un data lake dans S3 (importation, nettoyage, catalogage des données).  
	  - Gère la **sécurité et les accès** via un modèle de contrôle fin (table, colonne).  
	  - Automatisation des pipelines d’ingestion et de transformation (intégration avec Glue, EMR, Redshift, Athena).  
	  - Fait office de “couche de gouvernance” centralisée pour définir qui peut accéder à quelles données.  

- **Cas d’usage **:  
	  - **Centraliser les données** : consolider dans S3 différentes sources (RDS, NoSQL, fichiers on-premise…).  
	  - **Sécuriser fortement** : définir des règles d’accès granulaires (RGPD, HIPAA).  
	  - **Partage de datasets** : permettre à plusieurs équipes de consommer les mêmes données, avec des autorisations adaptées.  

- **Pour l’examen AWS SAA** :  
	  - Peut apparaître dans les questions relatives à la mise en place d’un **data lake** sécurisé, le catalogage et la gouvernance des données.  
	  - Se compare à la solution classique “Glue + IAM Policies + Bucket Policies” : Lake Formation va plus loin en offrant une gouvernance plus fine et centralisée.  
