# Other Services

## CloudFormation
- **Service d’infrastructure as code (IaC) managé par AWS**  
	  - Permet de **provisionner et gérer automatiquement des ressources AWS** via des fichiers de configuration  
	  - Tu décris ton infrastructure dans un **template (format YAML ou JSON)**  
	  - Tu lances ce template dans une **stack CloudFormation** → toutes les ressources sont créées et liées ensemble

- **Fonctionnalités clés**  
	  - **Déploiement reproductible et cohérent** : même template = même architecture à chaque fois  
	  - **Stack** = ensemble de ressources créées, modifiées ou supprimées en groupe  
	  - **Mise à jour incrémentale** possible avec des **Change Sets**  
	  - **Rollback automatique** en cas d’échec de création ou de mise à jour  
	  - Possibilité d’utiliser des **paramètres**, **mappings**, **conditions** et **outputs** pour **rendre les templates dynamiques et modulables**  
	  - Supporte **les dépendances** entre ressources → crée les ressources dans le bon ordre

- **Types de ressources provisionnables**  
	  - EC2, S3, RDS, VPC, IAM, Lambda, etc. (presque tous les services AWS)  
	  - Peut aussi intégrer des **stack imbriquées** (Nested Stacks) pour une meilleure organisation

- **Compléments et outils associés**  
	  - **AWS CloudFormation Designer** : outil visuel pour construire des templates  
	  - **Drift Detection** : identifie les différences entre l’état réel des ressources et la définition du template  
	  - Intégration avec **AWS CDK** (Cloud Development Kit) pour coder des stacks en TypeScript, Python, Java…

- **Cas d’usage**  
	  - **Déploiement automatisé** d’une infrastructure multi-service  
	  - Mise en place de **pipelines CI/CD** avec provisioning de l’infra  
	  - Gestion de **plusieurs environnements cohérents** (Dev, QA, Prod)  
	  - Mise en conformité avec des **standards d’architecture reproductibles**

- **Pour l’examen AWS SAA**  
	  - Notion **fréquente** dans les questions d’automatisation, de gestion d’environnements ou de DR  
	  - Bien retenir que CloudFormation permet de **gérer tout le cycle de vie** d’un ensemble de ressources  
	  - Savoir faire la différence entre **Stack, Template, Change Set, Drift**, et comprendre le principe de **Rollback automatique**

## Amazon SES

- **Service managé pour l’envoi et la réception d’emails sécurisé et scalable**  
	  - Permet d’**envoyer des emails transactionnels, marketing ou en masse**  
	  - Peut aussi être utilisé pour **recevoir des emails entrants**


- **Fonctionnalités clés**  
	  - **Support des standards d’authentification d’email** :  
		    - **DKIM (DomainKeys Identified Mail)**  
		    - **SPF (Sender Policy Framework)**  
	  - **Envoi d’e-mails** via :  
		    - **AWS Console**  
		    - **API SES**  
		    - **Protocole SMTP** (intégration simple avec les logiciels de messagerie ou applis existantes)

- **Monitoring et gestion de la réputation**  
	  - **Dashboard de réputation** intégré : observe la santé de ton envoi d’e-mails  
	  - **Statistiques détaillées** :  
		    - Nombre de **livraisons**  
		    - **Bounces** (échecs de livraison)  
		    - Résultats de la **boucle de rétroaction (feedback loop)**  
		    - **Taux d’ouverture** d’emails  
	  - Protection intégrée contre les **spams et abus**


- **Cas d’usage**  
	  - **E-mails transactionnels** : confirmation de commande, reset de mot de passe, notification de facture  
	  - **Campagnes marketing** (newsletters, promotions)  
	  - **Communications en masse** (notifications système, alertes)  
	  - **Réception d’e-mails automatisée** (ex : support@entreprise.com redirigé vers une fonction Lambda ou une règle S3)

- **Pour l’examen AWS SAA**  
	  - Notion **modérément fréquente**, utile à connaître pour les cas d’envoi automatisé ou d’intégration avec d'autres services  
	  - Retenir que SES **supporte SMTP**, **API**, **réception**, et **authentification DKIM/SPF**  
	  - Ne pas confondre avec SNS (notifications) ou WorkMail (messagerie collaborative)

## Amazon Pinpoint
- **Service managé pour la communication marketing bidirectionnelle à grande échelle**  
	  - Supporte **l’envoi et la réception** de messages (**2-way**)  
	  - Peut gérer **des milliards de messages par jour**

- **Canaux de communication supportés**  
	  - **Email**  
	  - **SMS** (transactionnel, marketing, en masse)  
	  - **Push notifications** (applications mobiles)  
	  - **Messages vocaux (voice)**  
	  - **Messages in-app**

- **Cas d’usage**  
	  - Campagnes marketing personnalisées  
	  - Notifications push ciblées (ex : abandon de panier, mise à jour d’une app)  
	  - **SMS en masse** pour événements, alertes ou authentification  
	  - Suivi des comportements utilisateurs et campagnes dans une **interface unifiée**

- **Comparaison avec Amazon SNS et Amazon SES**  
	  - Dans **SNS** ou **SES**, tu gères **chaque message manuellement** : audience, contenu, moment d’envoi  
	  - Dans **Pinpoint**, tu conçois des **campagnes complètes**, avec :  
		    - **Templates réutilisables**  
		    - **Segments utilisateurs dynamiques**  
		    - **Automatisation** de l’envoi (règles, déclencheurs, timing)  
		    - **Analyse comportementale intégrée**

- **Pour l’examen AWS SAA**  
	  - Moins fréquent que SES ou SNS, mais important à connaître pour les cas **d’automatisation marketing avancée**  
	  - Retenir que **Pinpoint = marketing multicanal avec segmentation, campagnes, tracking**  
	  - Bien différencier :  
		    - **SES** = envoi simple d’emails  
		    - **SNS** = notifications simples à des abonnés  
		    - **Pinpoint** = **plateforme complète de campagnes marketing avec ciblage et analytics**

## SSM Session Manager
  - Permet d’**ouvrir une session shell sécurisée** sur des instances **EC2 ou serveurs on-premise**, **sans SSH, ni port 22, ni clé SSH**  
  - **Plus sécurisé** : aucun port ouvert, aucune exposition à Internet  
  - **Compatible avec Linux, Windows, macOS**  
  - Possibilité de **journaliser toutes les sessions** dans **S3** ou **CloudWatch Logs** (audit, traçabilité, conformité)  
  - Fonctionne si l’instance a l’**agent SSM installé** et le bon **rôle IAM (SSMManagedInstanceCore)**



- **Autres fonctions clés de SSM**  

	  - **Run Command**  
		    - Exécute des **commandes à distance** sur une ou plusieurs instances, **sans SSH**  
		    - Idéal pour des tâches comme l’installation de logiciels, la gestion de fichiers, les scripts d’administration  

	  - **Patch Manager**  
		    - Permet d’**automatiser la gestion des mises à jour de sécurité** (OS) sur les instances EC2 ou on-premises  
		    - Tu définis des **règles de conformité** et des **plages horaires d’application**

	  - **Maintenance Windows**  
		    - Planifie des **fenêtres de maintenance automatiques** pour appliquer des **patchs**, **exécuter des scripts**, **lancer des automatisations**  
		    - Permet de respecter des **créneaux sans impact métier**

	  - **Automation**  
		    - Lance des **workflows d’automatisation** (pré-configurés ou personnalisés)  
		    - Exemples : **créer un AMI automatiquement**, **stocker une image avant update**, **restaurer une config**  
		    - Basé sur des **documents SSM (SSM Documents / Runbooks)**

- **Pour l’examen AWS SAA**  
	  - **Très fréquent**, surtout pour les questions liées à la **gestion d’instances sans SSH** ou à l’**automatisation système**  
	  - Bien retenir que **SSM remplace Bastion + SSH + gestion de clés**  
	  - Tu dois avoir un **IAM Role**, **VPC Endpoint** si en privé, et **SSM Agent actif**

## AWS Cost Explorer
- **Service pour visualiser, comprendre et gérer les coûts et l’usage AWS dans le temps**  
  	- Interface graphique simple pour analyser tes **factures AWS** et anticiper les coûts futurs  

- **Fonctionnalités clés**  
	  - Créer des **rapports personnalisés** sur :  
		    - **Coûts totaux** par service, par compte, par balise (tag)  
		    - **Usage** en fonction du temps (par heure, jour, mois)  
		    - **Niveau de détail élevé** : par **compte, service, région, ressource, instance**  
	  - Suivre l’évolution des coûts et identifier les **pics de consommation anormaux**

- **Optimisation**  
	  - Recommandations pour **Savings Plans** adaptés à ton profil de consommation  
	  - Aide à choisir le **plan le plus économique** pour tes workloads EC2, Lambda, Fargate, etc.

- **Prévisions**  
	  - **Prévision automatique jusqu’à 12 mois** basée sur l’historique d’usage  
	  - Tendance utile pour le **capacity planning**, les **budgets**, ou la **comptabilité prévisionnelle**

- **Pour l’examen AWS SAA**  
	  - Notion **modérément fréquente**, surtout dans les questions liées à **l’optimisation des coûts**  
	  - À retenir que **Cost Explorer donne une vue graphique détaillée des coûts**, contrairement à la simple facture  
	  - Savoir qu’il **propose des Savings Plans** et permet de **prévoir les dépenses futures**

## AWS Outposts
- **Solution de cloud hybride proposée par AWS**  
	  - Conçue pour les entreprises qui **conservent une infrastructure on-premises**, tout en utilisant les services AWS  
	  - Permet d’avoir une **expérience AWS identique sur site**, avec les **mêmes services, APIs, outils, console et CLI**

- **Fonctionnement**  
	  - AWS installe des **racks physiques (Outposts Racks)** directement dans ton datacenter  
	  - Ces racks sont **gérés à distance par AWS** (maintenance, monitoring, mises à jour)  
	  - Tu utilises les mêmes méthodes que dans le cloud public (CloudFormation, IAM, SDK, etc.)  
	  - Tu restes responsable de la **sécurité physique des équipements**

- **Avantages principaux**  
	  - **Latence ultra-faible** pour les applications qui ont besoin d’interagir avec d’autres systèmes sur site  
	  - **Traitement local des données sensibles** ou en temps réel  
	  - **Conformité à des exigences de résidence des données** (data residency)  
	  - **Facilite la migration vers le cloud** à ton rythme, avec les mêmes outils  
	  - **Service entièrement managé** (comme si c’était du cloud, mais sur place)

- **Cas d’usage**  
	  - Traitement de données sur site avec **besoin de faible latence** (industrie, santé, finance)  
	  - Applications devant respecter des **réglementations locales strictes**  
	  - Besoin de **continuité de services** en cas de perte de connectivité avec le cloud public

- **Pour l’examen AWS SAA**  
	  - **Notion à connaître** pour les scénarios **hybrides ou à faible latence**  
	  - Savoir que **AWS Outposts étend les services AWS sur site** avec une **gestion centralisée AWS**  
	  - Bien faire la distinction avec :  
		    - **Snow Family** (migration physique de données)  
		    - **Direct Connect / VPN** (connexion entre on-premise et cloud)  
		    - **Local Zones** (AWS infra déployée dans une ville spécifique)

## AWS Batch
- **Service managé pour exécuter des traitements par lots à grande échelle**  
	  - Permet de **lancer automatiquement des centaines de milliers de jobs batch**  
	  - Un job batch a **un début et une fin définis** (contrairement aux services en continu comme Lambda ou Kinesis)

- **Fonctionnement**  
	  - Tu **soumets ou planifies** tes jobs batch (via console, CLI ou API)  
	  - AWS Batch s’occupe de **provisionner dynamiquement les instances EC2 ou Spot Instances nécessaires**  
	  - Alloue automatiquement les ressources **CPU / RAM optimales** en fonction du besoin du job  
	  - Les jobs batch sont **emballés dans des images Docker** et s’exécutent via **ECS** (Elastic Container Service)

- **Avantages**  
	  - **Pas besoin de gérer manuellement l’infrastructure** : AWS s’occupe du provisioning, du scaling et de l’ordonnancement  
	  - Permet une **optimisation des coûts** en utilisant les **Spot Instances**  
	  - Compatible avec **n’importe quel runtime** si contenu dans une image Docker  
	  - Idéal pour des workloads comme :  
		    - Rendu vidéo  
		    - Calcul scientifique  
		    - Analyse de logs  
		    - Génération de rapports

- **Comparaison rapide AWS Batch vs AWS Lambda**

| **Critère**            | **AWS Lambda**                                         | **AWS Batch**                                              |
|------------------------|---------------------------------------------------------|-------------------------------------------------------------|
| **Type de workload**   | Événement court / réactif                              | Job défini avec un début et une fin                         |
| **Durée max**          | 15 minutes max                                         | **Aucune limite de durée**                                  |
| **Runtime**            | Limité à certains langages (Node.js, Python, etc.)     | **N’importe quel runtime via image Docker**                 |
| **Disque temporaire**  | 512 Mo (ephemeral storage par défaut)                  | **Stockage via EBS ou instance store**                      |
| **Type d’infra**       | **Serverless**, pas d’EC2                              | **Basé sur EC2 / ECS**, provisionné automatiquement         |


- **Pour l’examen AWS SAA**  
	  - Bien comprendre que **Batch ≠ Lambda** : Batch est fait pour **des workloads longs, lourds, custom**, via **Docker**  
	  - Retenir que **AWS gère les instances**, que ce soit **à la demande ou spot**  
	  - Utilisé quand on a **beaucoup de jobs indépendants à exécuter en parallèle**

## AWS AppFlow
- **Service managé d’intégration de données entre applications SaaS et AWS**  
	  - Permet de **transférer des données de manière sécurisée** entre des services comme **Salesforce, SAP, Zendesk, Slack, ServiceNow** et **des services AWS**  
	  - **Aucune ligne de code requise** → plus besoin d’écrire et maintenir des intégrations manuelles

- **Sources compatibles**  
	  - Applications SaaS courantes : **Salesforce, SAP, Zendesk, Slack, ServiceNow**  
	  - Autres connecteurs régulièrement ajoutés par AWS

- **Destinations possibles**  
	  - **AWS** : **S3**, **Redshift**, etc.  
	  - **Non-AWS** : **Salesforce**, **Snowflake**, etc.

- **Modes d’exécution**  
	  - **Planifié (schedule)** : ex. tous les jours à 2h  
	  - **Déclenché par événements** : ex. nouvelle opportunité dans Salesforce  
	  - **À la demande** : manuellement ou via API

- **Fonctionnalités supplémentaires**  
	  - **Transformation des données à la volée** :  
		    - **Filtrage**  
		    - **Validation**  
	  - **Sécurité** :  
		    - Données **chiffrées** pendant le transfert  
		    - Connexion **via Internet** ou de façon **privée avec AWS PrivateLink**

- **Cas d’usage**  
	  - Intégrer rapidement des **données SaaS dans S3** pour analyse (ex. logs de Zendesk)  
	  - Alimenter **Redshift ou Snowflake** avec des données CRM ou ERP sans écrire de pipeline complexe  
	  - Créer un **data lake ou un entrepôt de données** depuis plusieurs outils métier

- **Pour l’examen AWS SAA**  
	  - Moins fréquent, mais à connaître pour les **questions d’intégration SaaS → AWS**  
	  - Savoir que **AppFlow est une alternative simple aux ETL personnalisés**, sans code  
	  - Peut **éviter l’usage de Lambda + API + Glue** dans de nombreux cas simples

## AWS Amplify
- **Un ensemble d’outils et de services qui aide à développer et déployer des applications web et mobiles full-stack, évolutives**  
	  - Prise en charge de :  
		    - **Authentification**  
		    - **Stockage**  
		    - **API REST et GraphQL**  
		    - **CI/CD (déploiement continu)**  
		    - **Pub/Sub** (messagerie temps réel)  
		    - **Analytics**  
		    - **Prédictions IA/ML**  
		    - **Monitoring**

- **Connexion directe au code source** :  
	  - **GitHub**, **AWS CodeCommit**, **Bitbucket**, **GitLab**  
	  - Ou possibilité de **téléverser le code manuellement** depuis ton poste

## Instance Scheduler on AWS
- **Solution AWS déployée via CloudFormation (ce n’est pas un service natif AWS)**  
- Permet de **démarrer et arrêter automatiquement des ressources AWS** pour réduire les coûts (**jusqu’à 70 % d’économies**)  
- Exemple typique : **arrêter les instances EC2 en dehors des heures de bureau**

- **Ressources supportées** :  
	  - **Instances EC2**  
	  - **Groupes Auto Scaling EC2**  
	  - **Instances RDS**

- **Fonctionnement** :  
	  - Les **plannings sont gérés dans une table DynamoDB**  
	  - Utilise les **tags des ressources** et des fonctions **Lambda** pour effectuer le démarrage/l’arrêt  
	  - Compatible avec des ressources **cross-account** (multi-compte) et **cross-region** (multi-région)

