# AWS Monitoring & Audit

## CloudWatch Metrics
Amazon CloudWatch est un service de monitoring et d’observabilité centralisé pour les ressources et applications AWS (et même on-premises). Il collecte, stocke et analyse des métriques (CPU, RAM, requêtes, latence, etc.), des logs et des événements. CloudWatch Metric Streams est une fonctionnalité qui facilite l’envoi en temps quasi réel de métriques vers une destination choisie (par exemple un service analytique ou un SIEM). Voici les points clés :

- **CloudWatch Metrics**  
	  - Par défaut, beaucoup de **métriques standard** sont collectées par AWS (ex. CPUUtilization, NetworkIn, etc. pour EC2).  
	  - Possibilité de **métriques personnalisées** : on envoie soi-même des points de mesure (par ex. via l’API PutMetricData).  
	  - Paramétrage de **périodes** (1 minute, 5 minutes...), agrégation de données sur 15 mois (stockées pour rétrospective et alerting).  
	  - **Alarms** : on définit des seuils (ex. CPU > 80% sur 5 minutes) et déclenche des actions (SNS, autoscaling, etc.).  
	  - **Dashboards** : affichage visuel des métriques sur la console CloudWatch, partageable.  

- **CloudWatch Metric Streams**  
	  - Permet d’**exporter en continu** les métriques CloudWatch vers un **endpoint** (par ex. Kinesis Data Firehose, Kinesis Data Streams).  
	  - Données transmises au format Open Telemetry, en quasi temps réel.  
	  - Usage :  
	    - **Intégration analytique** : ingestion dans un data lake (S3), un SIEM, ou un moteur de streaming (Kinesis, Splunk, etc.).  
	    - **Monitoring avancé** : corrélation avec d’autres événements, analyse temps réel hors AWS.  
	  - Configuration simple : sélectionner les namespaces de métriques (EC2, Lambda, etc.), la fréquence (en secondes) et la destination.  

- **Cas d’usage**  
	  - **Supervision d’instances EC2** : alarms sur CPU, mémoire (métrique custom), logs (CloudWatch Logs), etc.  
	  - **Autoscaling** : déclencher l’ajout/retrait d’instances ou de tasks ECS en fonction des métriques.  
	  - **Audit / observabilité externe** : via Metric Streams, envoyer en temps réel des métriques vers un outil tiers d’analyse (Datadog, Splunk...).  

- **Pour l’examen AWS SAA**  
	  - Savoir que CloudWatch gère les **métriques**, **logs**, **alarms** et **dashboards**.  
	  - Identifier **Metric Streams** comme la solution pour **externaliser** la collecte de métriques en temps quasi réel.  
	  - Comparaison fréquente avec CloudTrail (pour les logs d’API calls) ou AWS X-Ray (pour le tracing des microservices).

## CloudWatch Logs
Amazon CloudWatch Logs est un service managé qui collecte, stocke, surveille et analyse les journaux applicatifs et systèmes provenant de différentes ressources AWS (EC2, Lambda, ECS, etc.) ou d'applications externes. CloudWatch Logs permet une gestion centralisée des logs, leur analyse en temps réel, ainsi que la configuration d'alarmes et de notifications.

- **Principales caractéristiques :**  
	  - **Centralisation des journaux** : stockage durable et sécurisé des logs provenant de multiples sources (EC2, Lambda, API Gateway, containers, etc.).  
	  - **Groupes et flux de journaux (Log Groups / Log Streams)** : classification structurée des logs pour une gestion claire.  
	  - **CloudWatch Logs Insights** : requêtes rapides et interactives sur les logs avec un langage de requête simple et efficace.  
	  - **Alarmes** : définition d'alarmes basées sur des modèles ou motifs précis détectés dans les logs.  
	  - **Rétention personnalisée** : choix de la durée de conservation (de quelques jours à plusieurs années).  
	  - **Exportation et intégration** : possibilité d’envoyer les logs vers S3, Elasticsearch/OpenSearch, Kinesis pour analyses avancées.  

- **Cas d’usage :**  
	  - **Analyse de logs centralisée** : centralisation des logs de plusieurs applications AWS pour simplifier leur gestion et la conformité.  
	  - **Monitoring d'applications et debug rapide** : rechercher rapidement des erreurs ou anomalies en temps réel via Logs Insights.  
	  - **Sécurité et audit** : alerter en temps réel sur des événements spécifiques (tentative d'accès non autorisé, erreurs critiques).  
	  - **Automatisation et analytics avancé** : exporter les logs vers S3 ou OpenSearch pour du reporting ou de l'archivage long terme.

- **Pour l’examen AWS SAA :**  
	  - Comprendre clairement son rôle par rapport à CloudTrail (qui surveille plutôt les appels d’API et actions AWS).  
	  - Savoir que CloudWatch Logs est la solution centrale pour gérer, stocker et analyser les logs applicatifs et d'infrastructure AWS.


## Cloudwatch Alarms
Amazon **CloudWatch Alarms** permet de surveiller des **métriques** (CPU, latence, erreurs, etc.) et de **déclencher des actions** (notifications, autoscaling, etc.) lorsqu’un **seuil** défini est atteint. Les **Composite Alarms** sont un sous-ensemble de CloudWatch Alarms qui utilisent la **logique booléenne** (AND/OR) sur plusieurs alarmes pour évaluer un état global. 

- **Principales caractéristiques :**  
	  - **Configuration de seuils** : par exemple « CPUUtilization > 80 % pendant 5 minutes ».  
	  - **Actions déclenchées** : envoi de messages via SNS, augmentation ou diminution d’instances (ASG), arrêt d’instances, etc.  
	  - **États des alarmes** : OK, ALARM, INSUFFICIENT_DATA (peut évoluer d’un état à l’autre en fonction de la disponibilité des métriques).  
	  - **Types d’Alarmes :**  
	    - **Standard Alarms** : surveillent une unique métrique ou un unique ensemble statistique.  
	    - **Composite Alarms** : combinent plusieurs alarmes déjà existantes en **AND / OR**, permettant par exemple de déclencher une action seulement si **toutes** les alarmes sont en ALARM (AND), ou si **au moins une** l’est (OR).  
	      - Ex. : déclencher un incident si « Alarme A (erreurs élevées) **ET** Alarme B (latence élevée) sont toutes en ALARM ».  
	  - **Périodes / Agrégations** : choix de la durée d’évaluation (1, 5, 15 minutes) et de la statistique (moyenne, max, min, sum…).  
	  - **Intégrations** : forte avec EC2, Auto Scaling Group, RDS, ECS, etc.  

- **Cas d’usage :**  
	  - **Surveillance automatique** : empêcher la saturation CPU, détecter une latence trop élevée, etc.  
	  - **Autoscaling** : ajouter ou supprimer des instances lorsqu’un seuil est franchi.  
	  - **Notifications** : alerter l’équipe DevOps (mail, SMS, Slack…) via SNS dès qu’une alarme se déclenche.  
	  - **Incidents complexes** : grâce aux **Composite Alarms**, ne déclencher une alarme globale que si plusieurs conditions sont remplies (ex. cumul CPU élevé + erreurs 5xx).  

- **Pour l’examen AWS SAA :**  
	  - Important de connaître le mécanisme de base (métrique, seuil, alerte) et son intégration avec **ASG, SNS**.  
	  - Les **Composite Alarms** sont parfois citées pour **éviter les faux positifs** ou affiner la condition de déclenchement.  


## EventBridge
Amazon EventBridge est un **bus d’événements** entièrement managé qui facilite le routage d’événements d’une source (service AWS, application SaaS tierce, application custom) vers une ou plusieurs destinations (Lambda, SQS, SNS, API, etc.). Il permet de construire des architectures **loosely coupled** et **serverless**, où les composants communiquent par événements plutôt que par appels directs.

- **Principales caractéristiques :**  
	  - **Sources multiples** : peut recevoir des événements de **plus de 100 services AWS** (EC2, S3, CloudWatch, etc.), d’applications SaaS (Zendesk, Salesforce…), ou de votre propre code (via l’API PutEvents).  
	  - **Routage flexible** : règles basées sur la **structure ou le contenu** de l’événement (filtres JSON). Vous pouvez rediriger les événements vers de multiples cibles (Lambda, Kinesis, Step Functions, SQS, SNS, API Gateway…).  
	  - **Schemas Registry** : stocke et gère les schémas des événements, facilitant l’autogénération de code pour les consommateurs.  
	  - **Moins de couplage** : vous n’avez pas besoin que la destination soit dispo ou connue à l’avance ; tout se fait par publication d’événements.  
	  - **Replay et archive** (optionnel) : EventBridge permet d’archiver et de rejouer les événements pour des cas de debug ou reprocessing.

- **Cas d’usage :**  
	  - **Architecture serverless événementielle** : déclencher une fonction Lambda quand un objet est uploadé dans S3, ou quand un message arrive depuis un outil SaaS.  
	  - **Intégration entre services internes et SaaS** : par ex, synchroniser automatiquement un événement Salesforce avec des microservices AWS.  
	  - **Automatisation et workflows** : déclencher Step Functions sur certains événements, filtrer ou transformer les données avant de lancer la suite.  
	  - **Monitoring avancé** : capturer des événements CloudTrail pour auditer en quasi temps réel.

- **Pour l’examen AWS SAA :**  
	  - Important de savoir qu’EventBridge permet de **connecter facilement** différents services ou applications via un **bus d’événements**.  
	  - Différence avec **SNS** : EventBridge est **plus axé sur le filtrage dynamique et la compatibilité SaaS**, tandis que SNS diffuse en “fan-out” plus simple.  
	  - Différence avec **CloudWatch Events** : en fait, EventBridge est l’évolution de CloudWatch Events et couvre plus de cas, notamment les sources SaaS.


## Cloudwatch Insights
Dans l’écosystème **Amazon CloudWatch**, plusieurs fonctionnalités spécifiques permettent de monitorer différents types d’applications et de ressources de façon plus ciblée :

- **Container Insights** :  
	  - Surveille la santé et les performances des clusters **ECS**, **EKS**, et Kubernetes on EC2.  
	  - Collecte des métriques détaillées (CPU, mémoire, rétentions de logs) et propose des **tableaux de bord** prêts à l’emploi.  
	  - Permet d’identifier plus rapidement les conteneurs ou pods en surcharge ou en panne.

- **Lambda Insights** :  
	  - Fournit des métriques et des logs avancés pour les **fonctions Lambda**.  
	  - Suit notamment l’utilisation CPU, la mémoire, la latence, le nombre d’invocations, et les éventuelles erreurs.  
	  - Facilite le **debug** et l’**optimisation** de la performance ou du coût de vos Lambdas.

- **Contributor Insights** :  
	  - Analyse des logs pour identifier les principaux **contributeurs** (adresses IP, utilisateurs, ressources) d’un comportement spécifique.  
	  - Affiche en temps quasi réel quels facteurs “contribuent” le plus à un certain métrique (ex. top IP qui génèrent des erreurs 4xx).  
	  - Aide à la détection rapide d’anomalies (surcharge, tentative d’attaque, etc.).

- **Application Insights** :  
	  - Conçu pour les **applications .NET et SQL Server** (mais s’étend à d’autres environnements) afin de détecter et diagnostiquer automatiquement les problèmes (bottlenecks, erreurs).  
	  - Fournit des **tableaux de bord** et des alertes sur la **santé** et la **performance** de l’application (temps de réponse, exceptions…).  
	  - Facilite les analyses post-incident en corrélant métriques, logs et événements.

Chacune de ces fonctionnalités vise à **simplifier le monitoring** en offrant des tableaux de bord et des **informations approfondies**, prêts à l’emploi, adaptés à la **nature** de vos workloads (conteneurs, serverless, logs, applications spécifiques, etc.). Elles s’intègrent toutes **nativement avec CloudWatch**, ce qui les rend faciles à activer et exploiter.

## CloudTrail
AWS CloudTrail est le service qui journalise **tous les appels d’API** (AWS CLI, console, SDKs) réalisés au sein de votre compte. Il facilite les audits et la traçabilité des actions. **CloudTrail Insights** est une fonctionnalité additionnelle qui **détecte et signale automatiquement les activités anormales** par rapport au comportement habituel, afin d’aider à repérer des pics soudains ou des anomalies dans l’utilisation du compte.

- **Principales caractéristiques** :  
	  - **Enregistrement d’appels d’API** : stockés par défaut dans un bucket S3, éventuellement relayés vers CloudWatch Logs ou un outil d’analyse.  
	  - **Multi-région & Multi-compte** : peut couvrir plusieurs régions et être centralisé au sein d’un même S3 bucket.  
	  - **Event History** : affiche les 90 derniers jours d’événements directement sur la console, pour une recherche rapide.  
	  - **Traçabilité fine** : indique l’heure, l’utilisateur, la commande exacte (ex: RunInstances), l’adresse IP, les clés d’accès, etc.

- **CloudTrail Insights** :  
	  - **Surveillance intelligente** : détecte les variations brusques dans la fréquence d’appels d’API (ex. un pic de CreateBucket inhabituel).  
	  - **Alertes & analyses** : vous pouvez mettre en place des actions (SNS, EventBridge) pour signaler ces anomalies.  
	  - **Permet l'investigation rapide** : isole les événements liés à un comportement anormal (tentative d’attaque, erreur de script, etc.).

- **Cas d’usage** :  
	  - **Audit et conformité** : prouver qui a fait quoi et quand, répondre aux exigences SOX, PCI, HIPAA, etc.  
	  - **Sécurité** : détecter en quasi temps réel des opérations suspectes, voire automatiser une réponse (ex: révoquer des accès, envoyer un message Slack).  
	  - **Diagnostic** : retracer pas à pas un incident de configuration ou de permission, et analyser un comportement anormal (d’où l’intérêt de CloudTrail Insights).  

- **Pour l’examen AWS SAA** :  
	  - Incontournable de connaître la différence entre **CloudWatch** (métriques, logs applicatifs) et **CloudTrail** (traçabilité des API calls).  
	  - **CloudTrail Insights** met en évidence les activités inhabituelles dans l’historique des appels d’API — une fonctionnalité à retenir pour la détection d’attaques ou d’anomalies.  

### CloudTrail - EventBridge Integration
AWS CloudTrail peut s’intégrer directement à **Amazon EventBridge** pour permettre des **réactions en temps quasi réel** aux appels d’API consignée par CloudTrail. En configurant un **EventBridge Rule** basé sur les événements CloudTrail, on peut filtrer certains types d’actions (ex : “DeleteBucket” ou “CreateSecurityGroup”) et **rediriger** ces événements vers diverses cibles (Lambda, SNS, SQS, Step Functions, etc.).

- Principales caractéristiques :  
	  - **Mécanisme de déclenchement automatisé** : dès qu’un événement CloudTrail correspond à un pattern précis (par ex. “Un utilisateur a modifié une politique IAM” ou “Quelqu’un a supprimé un bucket S3”), EventBridge exécute la règle et envoie l’événement à la cible choisie.  
	  - **Filtrage** : le **pattern JSON** dans EventBridge Rule autorise un filtrage riche (type d’action, nom de service, nom d’utilisateur, région…).  
	  - **Actions en cascade** : on peut, par exemple,  
		    - **Notifier** l’équipe DevOps via SNS,  
		    - **Lancer un Lambda** pour annuler automatiquement l’action suspecte ou pour loguer l’événement ailleurs,  
		    - **Invoquer Step Functions** pour un workflow d’approbation manuel, etc.  
	  - **Sécurité renforcée** : cette intégration permet de **détecter et réagir** plus rapidement à des appels d’API inattendus ou malveillants, sans attendre un examen différé des logs.

- Cas d’usage :  
	  - **Surveillance de changements sensibles** : ex. modifications IAM, suppression de ressources critiques, modifications de Security Groups.  
	  - **Flux de conformité** : archivage automatique de certains événements dans un système d’approbation ou un SIEM.  
	  - **Automatisation** : correction automatique (ex: recréer une ressource critique si elle a été supprimée, désactiver une clé d’accès, etc.).  

- Pour l’examen AWS SAA :  
	  - Savoir que **CloudTrail** + **EventBridge** permet un **mode événementiel** réactif, plus rapide qu’une simple inspection des logs CloudTrail.  
	  - Concurrence/Comparaison : on peut aussi coupler CloudTrail à **CloudWatch Event Rules** (ancien nom). Mais EventBridge est la version étendue, gérant plus de cas d’usage et de sources d’événements.  

## AWS Config
**AWS Config** est un service entièrement managé qui te permet de **surveiller, enregistrer et évaluer la configuration** de tes ressources AWS au fil du temps. Il fournit un **historique complet**, une **vue actuelle**, et une **traçabilité des changements** de configuration, ce qui en fait un outil essentiel pour la conformité, l’audit et le dépannage.

- **Surveillance continue de la configuration des ressources AWS**  
	  - Enregistre automatiquement les modifications sur les ressources (EC2, S3, IAM, SG, etc.)  
	  - Fournit une **vue actuelle** et un **historique complet** des états passés  
	  - Chaque changement est horodaté et associé à l’identité ayant effectué la modification  

- **Détection de non-conformité avec des règles**  
	  - Règles **AWS gérées** : ex. S3 doit être privé, instances EC2 doivent être dans un VPC  
	  - Règles **personnalisées** : via Lambda, permet d’adapter à sa propre politique interne  
	  - Évaluation continue ou sur demande de la conformité  
	  - En cas de non-conformité, possibilité de déclencher des **notifications** (SNS/EventBridge) ou une **remédiation automatique**

- **Analyse des changements**  
	  - Comparaison entre l’état actuel et les états précédents  
	  - Visualisation sous forme de timeline par ressource  
	  - Permet d’identifier la cause d’un dysfonctionnement ou d’un écart de configuration  

- **Centralisation multi-comptes et multi-régions**  
	  - Vue unifiée de la conformité via **AWS Organizations**  
	  - Agrégation des données dans un compte “auditeur”  

- **Intégration avec d’autres services**  
	  - **CloudTrail** : relie les changements de configuration aux appels d’API  
	  - **EventBridge** : déclenche des actions automatiques en cas de changement ou de non-conformité  
	  - **AWS Config Remediation** : peut automatiquement corriger les ressources non conformes avec Lambda  

- Cas d’usage :  
	  - **Audit et conformité** : suivi de conformité en temps réel avec normes (PCI-DSS, HIPAA, ISO...)  
	  - **Détection de dérives** : ex. bucket devenu public, Security Group ouvert sur 0.0.0.0/0  
	  - **Remédiation automatisée** : ex. suppression automatique d’une règle de firewall non conforme  
	  - **Analyse post-incident** : comprendre ce qui a changé juste avant un problème  

- Pour l’examen AWS SAA :  
	  - **Souvent présent** dans les questions de conformité et gouvernance  
	  - Ne pas confondre avec CloudTrail (traçage API) ou CloudWatch (monitoring de performance)  
	  - Bien connaître la **relation entre Config, CloudTrail et EventBridge**  

## CloudWatch vs CloudTrail vs Config

| **Service**       | **Rôle principal**                                           | **Ce qu’il suit**                                                 | **Type de données**                          | **Fréquence**         | **Cas d’usage typique**                                            |
|-------------------|--------------------------------------------------------------|--------------------------------------------------------------------|------------------------------------------------|------------------------|----------------------------------------------------------------------|
| **CloudWatch**    | **Surveillance et monitoring en temps réel**                 | Métriques (CPU, RAM, latence…), logs applicatifs, alarmes         | Numérique (stats, logs, dashboards)           | Temps réel             | Surveiller la performance, déclencher des alarmes, autoscaling     |
| **CloudTrail**    | **Audit des appels API (qui a fait quoi)**                   | Tous les appels d’API AWS (via console, CLI, SDK…)                | Fichiers de log JSON (événements API)         | Historique en quasi temps réel | Traçabilité, analyse de sécurité, audit d’activité                 |
| **AWS Config**    | **Surveillance de l’état et de la conformité des ressources**| L’état de configuration des ressources AWS (IAM, S3, EC2, etc.)    | États de ressources, règles de conformité     | À chaque changement ou de manière planifiée | Historique de config, détection de non-conformité, remédiation   |


- **CloudWatch** → *Comment ça fonctionne maintenant ?*  
  ➤ Donne des métriques, déclenche des alarmes, visualise des logs.

- **CloudTrail** → *Qui a fait quoi ?*  
  ➤ Trace tous les appels d’API dans le compte.

- **AWS Config** → *Quel est l’état de mes ressources ? Est-ce conforme ?*  
  ➤ Historise les changements de configuration et évalue la conformité.
