# Messaging & Integration

## SNS
- Service de **messagerie en mode publication/souscription (pub/sub)** qui permet d’**envoyer des messages en push à plusieurs destinataires (abonnés)** en même temps  
- Lorsqu’un **message est publié dans un topic SNS**, il est **automatiquement envoyé** à tous les abonnés associés au topic

- Un **topic SNS** est un canal logique de distribution  
- Un **éditeur (publisher)** publie un message dans le topic  
- Les **abonnés (subscribers)** peuvent être :  
	  - **Lambda**  
	  - **File SQS**  
	  - **Adresse email / SMS**  
	  - **Endpoint HTTP/HTTPS**  
	  - **Applications mobiles (Push)**

- Lorsqu’un topic SNS est **connecté à une file SQS** (ex : fan-out architecture), le message est copié dans la file SQS  
	  - Dans ce cas, la **gestion du message** est assurée par SQS  
	  - Après qu’un consommateur (ex : Lambda, EC2) **récupère un message depuis SQS**, ce message est temporairement **masqué** grâce au **Visibility Timeout**  
		    - Le **Visibility Timeout** est une période pendant laquelle le message **ne sera pas visible pour d'autres consommateurs**, afin d’**éviter qu’il ne soit traité en double**  
		    - Si le message n’est pas supprimé avant la fin du délai, il **redevient visible et peut être réassigné à un autre consommateur**  
		    - **Par défaut : 30 secondes**, **min : 0 s**, **max : 12 heures**  
		    - Augmenter cette valeur donne plus de temps au consommateur pour **terminer son traitement sans duplication**

- SNS ne stocke pas les messages, il les **envoie immédiatement aux abonnés** ; si l’abonné n’est pas disponible, le message est **perdu**, sauf si c’est une **file SQS** ou une **Lambda avec retry**


- Pour l’examen AWS SAA :  
	  - Très courant, notamment dans les architectures **événementielles**, ou pour le **routing de notifications multi-destinataires**  
	  - Bien retenir :
		    - **SNS = push**, **SQS = pull**  
		    - **Visibility Timeout = mécanisme de protection contre le traitement multiple d’un message dans SQS**  
		    - **Fan-out pattern** : SNS publie une fois → plusieurs SQS reçoivent le même message

## SQS
- Service de **messagerie asynchrone** qui permet à des composants d’application de **communiquer de manière découplée**  
- Fonctionne selon le modèle **pull** : un **producteur** envoie un message dans une file (queue), un **consommateur** vient **le lire et le traiter plus tard**

- Permet d’**absorber des pics de charge**, d’améliorer la **résilience** et de **découpler les services** (ex : frontend / backend)  
- Le message peut contenir jusqu’à **256 Ko** (SQS standard)  
- Les messages peuvent être **chiffrés au repos avec SSE-KMS** et en **transit via HTTPS**

- Deux types de file SQS :
	  - **Standard Queue** :
		    - **Haute disponibilité**, **scalabilité illimitée**
		    - **Livraison au moins une fois** (possibilité de duplication)
		    - **Ordre non garanti**
	  - **FIFO Queue** :
		    - **First-In-First-Out**, **ordre strict garanti**
		    - **Pas de duplication** (exactly-once delivery)
		    - Débit plus limité que les files standard (jusqu’à 300 msgs/sec ou 3000 avec batching)

- Une fois le message lu, il est masqué temporairement grâce au **Visibility Timeout** :
	  - C’est une période durant laquelle **le message n’est plus visible** par d’autres consommateurs  
	  - Permet au consommateur actuel de **terminer le traitement sans risque de duplication**
	  - **Par défaut : 30 secondes**, **min : 0 s**, **max : 12 heures**
	  - Si le message **n’est pas supprimé avant expiration**, il redevient visible et peut être **traité de nouveau**

- Possibilité de configurer une **DLQ (Dead Letter Queue)** pour capturer les messages qui échouent plusieurs fois  
- Compatible avec des architectures **serverless** (ex : déclenchement d’une **Lambda** sur réception d’un message)

- Peut être combiné avec **SNS** pour le pattern fan-out :  
	  - SNS publie un message → SQS reçoit ce message comme abonné  
	  - Idéal pour diffuser un message vers plusieurs files consommateurs


- Pour l’examen AWS SAA :
	  - Très fréquent, souvent opposé à SNS et Kinesis
	  - Bien retenir :
		    - SQS est **pull** (contrairement à SNS qui est **push**)  
		    - FIFO vs Standard : ordre, duplication, débit  
		    - Visibility Timeout : **évite le traitement multiple** d’un message  
		    - DLQ : **récupération des messages problématiques**  
		    - Peut **déclencher une Lambda directement**


## Amazon Kinesis Data Firehose VS Kinesis Data Streams

| **Critère**                        | **Kinesis Data Streams**                      | **Kinesis Data Firehose**                            |
|-----------------------------------|----------------------------------------------|------------------------------------------------------|
| **Type de service**               | Data streaming (temps réel)                  | Ingestion & livraison automatisée (quasi temps réel)|
| **Niveau de gestion**             | Semi-géré : tu gères les shards & consommateurs | Full-managed (zéro infra à gérer)                   |
| **Latence**                       | ~200 ms                                      | 1 à 5 minutes environ                                |
| **Transformation des données**    | Personnalisée via app Lambda/EC2             | Intégrée via Lambda (facultatif)                    |
| **Stockage intermédiaire**        | Données retenues 24h à 7 jours (ou +)        | Pas de stockage réel (stream direct vers cible)     |
| **Destinations possibles**        | Traitement personnalisé                      | S3, Redshift, OpenSearch, Splunk, Datadog…          |
| **Cas d’usage typique**           | Monitoring temps réel, custom analytics      | Livrer des logs ou données brutes vers S3/Redshift  |

## SQS vs SNS vs Kinesis

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
