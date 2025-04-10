# Distaster Recovery & Migration

## Distaster Recovery

| **Stratégie DR**         | **Description**                                                                 | **RTO**          | **RPO**          | **Coût**         | **Remarques**                                                                 |
|--------------------------|----------------------------------------------------------------------------------|------------------|------------------|------------------|-------------------------------------------------------------------------------|
| **Backup & Restore**     | Sauvegardes régulières, restauration manuelle après incident                    | Élevé (heures)   | Élevé (dernière sauvegarde) | Faible           | Bonne pour les environnements non critiques ou à budget serré                |
| **Pilot Light**          | Composants critiques (ex : DB) toujours actifs, le reste lancé à la demande     | Moyen (minutes-heures) | Faible           | Modéré           | Bon compromis coût/rapidité ; l’infra est prête mais en mode réduit          |
| **Warm Standby**         | Environnement réduit déjà opérationnel, il suffit de scaler en cas de sinistre | Faible (minutes) | Faible           | Moyen à élevé    | Déploiement rapide possible sans recréer toute l’infra                        |
| **Multi-Site (Hot Site)**| Environnements actifs simultanément dans plusieurs régions (Active-Active)     | Très faible (~0) | Très faible (~0) | Élevé            | Pas d’interruption, haute disponibilité assurée                               |

- **RTO (Recovery Time Objective)** : délai maximal toléré pour **restaurer le service**
- **RPO (Recovery Point Objective)** : perte de données maximale tolérée (temps entre dernière sauvegarde et incident)

- **Backup and Restore**  
	  - Stratégie la plus simple et la plus économique  
	  - Tu effectues régulièrement des **sauvegardes (snapshots, exports)** de tes données et configurations (ex : S3, RDS, EBS)  
	  - En cas de sinistre, tu **restaures manuellement** les ressources dans une autre région ou AZ  
	  - **Temps de récupération élevé (RTO élevé)**, mais **coût très faible**

- **Pilot Light**  
	  - Tu gardes une **infrastructure minimale toujours allumée** (ex : base de données répliquée, configuration prête)  
	  - En cas de sinistre, tu **lances rapidement les serveurs d’application** ou autres ressources manquantes  
	  - Bon compromis entre **coût et rapidité de récupération**  
	  - **RTO moyen**, **coût modéré**

- **Warm Standby**  
	  - Une **version réduite mais fonctionnelle** de ton environnement tourne en permanence dans une autre région ou AZ  
	  - Moins de puissance (ex : EC2 en plus petite taille), mais **préconfigurée et opérationnelle**  
	  - En cas de besoin, tu **scale up immédiatement** pour restaurer la pleine capacité  
	  - **RTO faible**, **coût moyen à élevé**

- **Multi-Site / Hot Site (Active-Active)**  
	  - Ton système est **entièrement répliqué et actif** dans deux ou plusieurs régions ou AZ  
	  - Les utilisateurs peuvent être redirigés via **Route 53, ELB, ou failover automatique**  
	  - **Haute disponibilité assurée**, sans interruption perceptible  
	  - **RTO très faible (quasi nul)**, mais **coût élevé**

- **À retenir pour l’examen AWS SAA**  
	  - Ces stratégies apparaissent souvent dans les questions liées à la **tolérance aux pannes, à la disponibilité, et aux coûts**  
	  - Plus le **RTO (Recovery Time Objective)** est court, plus la solution est **coûteuse**  
	  - **Backup & Restore** est lent mais économique ; **Hot Site** est rapide mais cher  
	  - **Route 53** est souvent utilisé pour le **basculement DNS entre régions**

### Tips
- **Backup**  
	  - Sauvegardes automatiques ou manuelles :  
	    	- **EBS Snapshots**, **RDS automated backups / snapshots**  
	  - Archivage dans S3 ou Glacier :  
		    - **Push réguliers vers S3, S3 IA ou Glacier**  
		    - Utiliser des **Lifecycle Policies** pour automatiser l’archivage  
		    - Activer la **réplication cross-région S3 (CRR)** pour plus de résilience  
	  - Pour l’on-premise :  
	    	- Utiliser **AWS Snowball** ou **AWS Storage Gateway** pour sauvegarder vers AWS

- **High Availability (HA)**  
	  - **Route 53** pour le **basculement DNS entre régions**  
	  - Services managés multi-AZ :  
	    	- **RDS Multi-AZ**, **ElastiCache Multi-AZ**, **EFS**, **S3 (redondance régionale)**  
	  - Utiliser **Site-to-Site VPN** comme plan B si **Direct Connect** tombe

- **Replication**  
	  - Bases de données :  
		    - **RDS Cross-Region Replication**, **Aurora Global Databases**  
		    - **Réplication depuis on-premise vers RDS (via DMS ou outils natifs)**  
	  - **Storage Gateway** : réplique localement vers AWS et inversement

- **Automation**  
	  - Utiliser **CloudFormation**, **Elastic Beanstalk** ou **Terraform** pour **recréer rapidement l’environnement**  
	  - **CloudWatch + Auto Recovery** : redémarrage automatique des EC2 si une alarme échoue  
	  - **Lambda** pour exécuter des tâches automatiques personnalisées (remédiation, redéploiement, notification, etc.)

- **Chaos Engineering**  
	  - S’inspirer de Netflix avec son “**Simian Army**”  
		    - Outils qui **terminent aléatoirement des EC2** pour tester la résilience  
		    - Idéal pour valider un plan DR et la tolérance aux pannes

## Database Migrations Services
- **Service managé pour migrer rapidement et en toute sécurité des bases de données vers AWS**  
	  - Migration **résiliente** et **auto-réparatrice (self-healing)**  
	  - La base source **reste disponible pendant la migration** (pas de downtime majeur)

- **Types de migrations prises en charge**  
	  - **Homogènes** : même moteur source et cible (ex : **Oracle → Oracle**, **MySQL → MySQL**)  
	  - **Hétérogènes** : moteurs différents (ex : **SQL Server → Aurora**, **Oracle → PostgreSQL**)  
	    - Dans ce cas, il faut aussi utiliser **AWS Schema Conversion Tool (SCT)** pour adapter la structure

- **Réplication continue avec CDC (Change Data Capture)**  
	  - Capte les **changements en temps réel** pendant et après la migration initiale  
	  - Permet une **migration quasi sans interruption**, jusqu’au basculement final

- **Architecture du service**  
	  - Nécessite une **instance EC2 DMS (replication instance)**  
		    - Elle exécute la **tâche de réplication**  
		    - Peut être provisionnée et surveillée automatiquement  
	  - La source et la cible peuvent être **on-premise**, **dans AWS**, ou **entre régions AWS**

- **Cas d’usage**  
	  - Migration **on-premise vers AWS** (lift and shift)  
	  - Migration vers **Aurora, RDS, Redshift**  
	  - **Réplication en continu** entre deux bases pour synchronisation  
	  - Migration **sans arrêt de service** dans un projet de transformation cloud


- **Pour l’examen AWS SAA**  
	  - **Très probable** dans les questions de migration de bases de données  
	  - Bien retenir que :  
		    - **DMS = service managé avec réplication active**  
		    - **La base source reste en ligne** pendant la migration  
		    - **Instance EC2 DMS requise** pour effectuer les tâches  
		    - Utilise **CDC** pour synchroniser les modifications après migration initiale  
		    - Pour les migrations hétérogènes, il faut aussi utiliser **SCT**

## On-Premise strategy with AWS

| **Service AWS**                        | **Objectif**                                                                 | **Type de migration**                         | **Spécificités / Avantages**                                                   |
|----------------------------------------|------------------------------------------------------------------------------|-----------------------------------------------|---------------------------------------------------------------------------------|
| **Amazon Linux 2 AMI (format VM)**     | Exécuter Amazon Linux localement                                            | Simulation / compatibilité                     | Téléchargement pour VMware, VirtualBox, KVM, Hyper-V                          |
| **VM Import / Export**                 | Migrer ou restaurer des VMs                                                 | **VM → EC2** / **EC2 → VM**                   | Supporte les formats .ova/.vmdk, création d’AMIs depuis VMs                    |
| **AWS Application Discovery Service**  | **Analyser l’infrastructure on-premise** pour planifier une migration       | Aucune migration directe                       | Collecte CPU/RAM/OS, dépendances applicatives                                 |
| **AWS Migration Hub**                 | **Suivre et centraliser toutes les migrations**                             | Multi-service (DMS, SMS, etc.)                | Vue unifiée, tableau de bord multi-comptes                                     |
| **AWS Database Migration Service (DMS)** | Migrer et synchroniser des bases de données                                | **DB On-prem → AWS**, AWS → AWS, AWS → On-prem | Réplication continue (CDC), homogène ou hétérogène, moteur DB varié           |
| **AWS Server Migration Service (SMS)** | **Migrer des serveurs physiques ou virtuels** vers AWS                      | **Serveur (VM/physique) → EC2**               | Réplication incrémentale, création automatique d’AMIs                          |
| **AWS Storage Gateway**               | Étendre le stockage local vers AWS                                          | Backup / cache / accès fichier                 | NFS/SMB vers S3, mode File/Volume/Tape                                          |
| **AWS Snowball / Snowcone**           | Transférer de gros volumes de données                                      | **Data Center → AWS (physique)**              | Dispositifs sécurisés pour migration hors ligne massive                        |


- **Exécuter Amazon Linux 2 localement**  
	  - Possibilité de **télécharger l’AMI Amazon Linux 2 au format .iso / .ova**  
	  - Compatible avec les hyperviseurs **VMware, KVM, VirtualBox, Hyper-V**

- **VM Import / Export**  
	  - Permet de **migrer des machines virtuelles on-premises vers EC2**  
	  - Inversement, tu peux **exporter des instances EC2 vers ton environnement local**

- **Cas d’usage**  
	  - **Lift-and-shift** d’applications vers EC2  
	  - **Plan de reprise après sinistre (DR)** : créer un **référentiel EC2** prêt à relancer tes VMs  
	  - **Test cloud** de tes applications locales avant migration complète

- **Analyse et planification de migration**  
	  - **AWS Application Discovery Service** :  
		    - Scanne ton environnement pour **collecter les infos système** (RAM, CPU, OS…)  
		    - Fournit une **cartographie des dépendances** (qui parle à quoi)  
		    - Nécessite un agent sur les serveurs à analyser (ou version sans agent)  
	  - **AWS Migration Hub** :  
		    - Centralise et **suit l’état de progression** de toutes les migrations (VM, base de données...)  
		    - Fournit une **vue globale multi-outils** (DMS, SMS, etc.)

- **Migration de bases de données (DMS)**  
	  - **AWS Database Migration Service**  
	    - Réplication **on-premise → AWS**, **AWS → AWS**, ou **AWS → on-premise**  
	    - Prend en charge de nombreux moteurs : **Oracle, SQL Server, MySQL, PostgreSQL, DynamoDB…**  
	    - Utilise le **CDC (Change Data Capture)** pour une synchronisation en temps réel

- **Migration de serveurs complets (SMS)**  
	  - **AWS Server Migration Service (SMS)**  
		    - Fait de la **réplication incrémentale** de serveurs physiques ou virtuels vers AWS  
		    - Permet de **garder le serveur on-premise actif pendant la migration**  
		    - Génère automatiquement des AMIs prêtes à lancer sur EC2

- **Pour l’examen AWS SAA**  
	  - Important de **connaître les outils adaptés à chaque type de migration** :  
		    - **VM Import/Export** pour déplacer des VMs  
		    - **SMS** pour migration de serveurs complets (incrémental)  
		    - **DMS** pour les bases de données (avec ou sans changement de moteur)  
		    - **Application Discovery Service** pour planification préalable  
		    - **Migration Hub** pour le suivi

## AWS Backup
- **Service managé de sauvegarde centralisée**  
	  - Permet de **gérer, planifier et automatiser les backups** sur plusieurs services AWS  
	  - **Pas besoin de scripts maison ni de tâches manuelles**

- **Services pris en charge**  
	  - **EC2 / EBS**  
	  - **S3**  
	  - **RDS (tous les moteurs)** / **Aurora** / **DynamoDB**  
	  - **DocumentDB** / **Neptune**  
	  - **EFS** / **FSx for Windows** / **FSx for Lustre**  
	  - **AWS Storage Gateway** (Volume Gateway uniquement)

- **Fonctionnalités clés**  
	  - **Sauvegardes planifiées ou à la demande**  
	  - **Plans de sauvegarde (Backup Plans)**  
		    - Fréquence : **toutes les 12h, quotidienne, hebdo, mensuelle ou cron**  
		    - **Fenêtre de backup** configurable  
		    - **Politique de transition vers le stockage froid** : jamais, après X jours/semaines/mois/années  
		    - **Période de rétention** configurable : pour combien de temps garder les sauvegardes  
	  - **Politiques basées sur les tags** : appliquer un plan à toutes les ressources avec un tag donné  
	  - **Support du PITR (Point-In-Time Recovery)** pour les services compatibles  
	  - **Restauration inter-région (cross-region)** possible  
	  - **Support des sauvegardes inter-comptes (cross-account)**

- **Pour l’examen AWS SAA**  
	  - Bien retenir que c’est une **solution centralisée** pour automatiser les sauvegardes dans AWS  
	  - Utile pour répondre aux exigences de **compliance, sécurité, et reprise après sinistre**  
	  - Savoir que **S3 est supporté**, ce qui est récent  
	  - Attention à la **distinction avec les snapshots manuels, les sauvegardes RDS natives, ou les exportations manuelles**

