# Machine Learning
## Overview

| **Service**      | **Description**                                                                                     |
|------------------|-----------------------------------------------------------------------------------------------------|
| **Amazon Rekognition** | Détection de visages, étiquetage d’images (objets, scènes), reconnaissance de célébrités.  |
| **Amazon Transcribe** | Conversion de l’audio en texte (sous-titres, retranscription d’appels ou de podcasts).        |
| **Amazon Polly** | Synthèse vocale : convertit du texte en audio naturel.                                              |
| **Amazon Translate** | Traduction automatisée de texte dans de nombreuses langues.                                     |
| **Amazon Lex**   | Création de chatbots conversationnels (voice & text), basé sur les mêmes technologies qu’Alexa.     |
| **Amazon Connect** | Service de centre de contact (call center) cloud, intégrable avec Lex pour IVR intelligent.       |
| **Amazon Comprehend** | Analyse de texte (entités, sentiment, mots-clés), Natural Language Processing (NLP).          |
| **Amazon SageMaker**  | Plateforme de machine learning managée, pour entraîner et déployer des modèles ML.            |
| **Amazon Kendra**     | Moteur de recherche intelligent, exploitant le ML pour offrir des résultats pertinents.        |
| **Amazon Personalize** | Recommandations personnalisées en temps réel (ex: e-commerce, médias).                        |
| **Amazon Textract**   | Extraction de texte et de données structurées (tables, formulaires) depuis des documents scannés. |

## Amazon Rekognition
Amazon Rekognition est un service d’analyse d’images et de vidéos entièrement managé, utilisant le Machine Learning pour identifier des objets, des scènes, des visages, ou même du texte dans les images. Il permet également la détection des émotions faciales, la modération de contenu et la comparaison de visages.

- **Principales caractéristiques :**  
	  - **Détection d’objets et de scènes** : par exemple, reconnaître la présence de voitures, de personnes, d’animaux, etc.  
	  - **Analyse de visages** : reconnaissance de traits (émotions, yeux ouverts/fermés, barbe…), détection de similarités entre plusieurs visages.  
	  - **Lecture de texte (OCR)** : extraire du texte depuis des images (panneaux, documents scannés…).  
	  - **Modération de contenu** : identifier si une image ou une vidéo contient du contenu explicite, violent, etc.  
	  - **Analyse vidéo en temps quasi réel** (via Amazon Kinesis Video Streams ou en batch).  

- **Cas d’usage :**  
	  - **Sécurité et accès** : app de contrôle d’identité par reconnaissance faciale.  
	  - **Retail / e-commerce** : recherche d’images similaires (par exemple, comparer des produits).  
	  - **Médias & divertissement** : modération de contenu, catalogage automatique de vidéos (qui apparaît dans la scène, quels objets y figurent ?).  
	  - **Marketing / Réseaux sociaux** : filtre de contenu, tag automatique, détection de visages/émotions.  

- **Pour l’examen AWS SAA :**  
	  - Rarement abordé en détail, mais peut apparaître pour **des scénarios IA/ML simples** (transformation d’images, automatisation).  
	  - On peut tester ta capacité à connaître les services de reconnaissance ML managés d’AWS (Rekognition, Transcribe, Polly, Translate…).  

## Amazon Transcibe
Amazon Transcribe est un service d’**Automatic Speech Recognition (ASR)** entièrement managé. Il convertit de l’audio en texte, et peut également prendre en charge la transcription de conversations ou d’appels téléphoniques en temps quasi réel. Voici ses points principaux :

- **Principales caractéristiques** :  
	  - **Reconnaissance vocale** : prend un fichier audio (ou un flux) et produit du texte transcrit.  
	  - **Multilingue** : prend en charge plusieurs langues, avec détection automatique dans certains cas.  
	  - **Personnalisation du vocabulaire** : possibilité d’ajouter des mots spécifiques (noms de produits, argot, termes techniques).  
	  - **Mode streaming** : transcription presque en direct (par exemple, pour des appels au service client).  
	  - **Mode batch** : traitement d’un fichier audio dans S3.  
	  - **Horodatage** : chaque mot transcrit est associé à un timecode.  
	  - **Reconnaissance d’enceinte (Speaker Diarization)** : identification des différents interlocuteurs (utile pour les réunions, podcasts, etc.).  

- **Cas d’usage** :  
	  - **Service client** : transcription automatique d’appels, analyse de la satisfaction.  
	  - **Médias** : sous-titrage automatique de vidéos ou de podcasts.  
	  - **Saisie automatique de compte-rendus** : réunions d’entreprise, événements, interviews.  
	  - **Accessibilité** : rendre du contenu audio disponible en version texte.  

- **Pour l’examen AWS SAA** :  
	  - Rare qu’on aborde Transcribe de manière approfondie, mais il peut apparaître dans des scénarios de **traitement IA/ML** (par exemple, comparé à Amazon Rekognition, Polly, Translate).  
	  - Retenir son rôle : **conversion audio → texte**.  

## Amazon Polly
Amazon Polly est un service de synthèse vocale managé, capable de convertir du texte en paroles réalistes dans de nombreuses langues et avec divers styles de voix (voix féminine, masculine, voix enfant, etc.). Voici ses points clés :

- **Principales caractéristiques** :  
	  - **Synthèse vocale en temps quasi réel** : création de fichiers audio ou streaming direct.  
	  - **Large panel de langues et de voix** : choix de l’accent, du genre, des variantes.  
	  - **Voix neurales** (Neural TTS) : intonation et fluidité plus naturelles.  
	  - **Fonction SSML** : possibilité d’ajuster la prononciation, la vitesse, les pauses via un langage balisé.  
	  - **Intégrations multiples** : diffusion audio, génération de podcasts, applications de guidage vocal.  
	  - **Tarification** pay-as-you-go : facturation au nombre de caractères traités.

- Cas d’usage :  
	  - **Accessibilité** : lecture de textes pour des personnes malvoyantes ou ayant des troubles de la lecture.  
	  - **Assistants vocaux** : apps ou sites nécessitant une voix de synthèse (chatbots, kiosques).  
	  - **Contenu multimédia** : génération de voix-off pour des vidéos ou du e-learning.  
	  - **Annonces automatisées** : signalétique vocale, alertes, systèmes d’information.  

- **Pour l’examen AWS SAA** :  
	  - Peu abordé en profondeur, mais retenez que Polly est **text-to-speech**.  
	  - Peut se comparer à Rekognition (image/vidéo), Transcribe (speech-to-text), Translate (traduction), etc.  

## Amazon Translate 
Amazon Translate est un service de traduction automatique neuronale, entièrement managé et facile à intégrer. Il permet de traduire des textes en plusieurs langues, avec une qualité supérieure aux approches traditionnelles de traduction automatique (statistique).  
- **Principales caractéristiques** :  
	  - Reconnaissance automatique de la langue source, prise en charge de dizaines de langues (dont le français, l’anglais, l’espagnol, l’arabe, le chinois, etc.).  
	  - Service serverless, aucune infrastructure à gérer.  
	  - Option de personnalisation légère du vocabulaire (Custom Terminology).  
	  - Intégration possible avec d’autres services AWS (ex: Lambda, S3, CloudWatch).  
	  - Pay-per-use, facturation basée sur le volume de texte traduit (caractères).  

- **Cas d’usage** :  
	  - Communication multilingue : sites web, applications, support client en temps quasi réel.  
	  - Globalisation des contenus : traduire documentation, articles, fiches produits pour toucher un public mondial.  
	  - Analyses ou feedbacks internationaux : agrégation de retours clients dans différentes langues.  

- **Pour l’examen AWS SAA** :  
	  - Rarement abordé en détail, mais on peut t’interroger sur l’existence de ce service dans un scenario IA/ML qui nécessite une **traduction automatique**.  
	  - Compare souvent aux autres services ML managés : Rekognition (images), Comprehend (analyse de texte), Transcribe (audio→texte), Polly (texte→audio).  

## Amazon Lex & Connect
Amazon Lex est un service de création de chatbots conversationnels (voice & text) qui exploite les mêmes technologies de reconnaissance vocale et de compréhension du langage naturel que Alexa. Amazon Connect est un service cloud de centre de contact qui permet de gérer des appels entrants, sortants et des interactions via différents canaux (voix, chat). Les deux s’intègrent parfaitement pour fournir une expérience de service client automatisée ou partiellement automatisée.

- **Amazon Lex** :  
  	- **Principales caractéristiques :**
	    - Permet de créer facilement des « intents » (intentions utilisateur) et des « slots » (variables) pour identifier les requêtes et recueillir des informations.  
	    - Génère automatiquement une interface conversationnelle (chat ou voix) avec les mêmes technologies qu’Alexa.  
	    - Intégration fluide avec Lambda, DynamoDB, entre autres.  
	    - Adapté pour des scenarios de FAQ, commande de produits, traitement d’informations, etc.  
	  - Cas d’usage :  
	    - Créer des bots de support ou de commande 24/7.  
	    - Automatiser une partie du service client (prise de rendez-vous, suivi de colis…).  
	    - Intégration dans les interfaces de messagerie (Slack, Facebook Messenger).  
	  - Pour l’examen AWS SAA :  
	    - Peu abordé, mais Lex est le service type pour réaliser un chatbot en mode serverless.  


- **Amazon Connect** :  
  	- **Principales caractéristiques** :  
	    - Centre de contact omnicanal managé : gestion des appels entrants/sortants, interactions chat, intégrations CRM.  
	    - Pay-per-use (minutes + utilisateurs connectés).  
	    - Personnalisation poussée via des flux de contacts configurables (call flows).  
	    - Intégration possible avec Lex (pour un IVR intelligent), Lambda (logique métier), DynamoDB (stockage données clients).  
	    - Enregistrements d’appels, analyses en temps réel via Contact Lens (option d’analyse sentimentale).  
	  - Cas d’usage :  
	    - Mettre en place un call center complet sans gérer l’infrastructure téléphonique.  
	    - IVR (Interactive Voice Response) automatisée, suivi client, transferts d’appels.  
	    - Rappels programmés, distribution intelligente des appels, intégration CRM.  
	  - Pour l’examen AWS SAA :  
	    - Rare, mais peut être mentionné comme la solution de call center managé d’AWS, surtout couplée à Lex pour l’automatisation.  

## Amazon Comprehend
Amazon Comprehend est un service d'analyse de texte basé sur le machine learning, qui permet d’extraire des informations et d'identifier le sens (sentiment, entités, sujets) depuis des documents ou des flux textuels. Voici les points à retenir :

- **Principales caractéristiques** :  
	  - **Analyse de sentiment** : repère si un texte est négatif, positif, neutre ou mixte.  
	  - **Reconnaissance d’entités** : identifie des éléments tels que noms de personnes, d’organisations, lieux, etc.  
	  - **Key Phrases Extraction** : détecte les expressions clés au sein du texte.  
	  - **Language Detection** : détecte la langue utilisée dans un texte donné.  
	  - **Syntax Analysis** : analyse la structure grammaticale (verbes, noms, adjectifs…).  
	  - **Custom Comprehend** : possibilité d’entraîner des modèles spécifiques pour des entités ou des catégories personnalisées.  

- **Cas d’usage** :  
	  - **Analyse de feedbacks clients** (emails, avis, tickets support) pour en déduire le ressenti global.  
	  - **Fouille de documents juridiques, médicaux** pour en extraire des entités ou termes-clés.  
	  - **Classification de texte** (thématique, détection de sujet).  
	  - **Moteur de recommandation** basé sur des mots-clés dans les critiques produits ou articles.  

- **Pour l’examen AWS SAA** :  
	  - Rarement abordé en profondeur, mais vous pouvez y être confronté dans une question concernant l’IA appliquée au texte.  
	  - Compare à **Rekognition** (images), **Transcribe** (audio→texte), **Polly** (texte→voix), **Translate** (traduction).  

## Amazon Comprehend Medical
Amazon Comprehend Medical est une version spécialisée d’**Amazon Comprehend** conçue pour analyser du texte médical (comptes rendus, documents cliniques, prescriptions, etc.). Elle détecte automatiquement des entités et des concepts liés à la santé, tout en respectant des exigences spécifiques de confidentialité et de conformité. Voici les points essentiels :

- **Principales caractéristiques :**  
	  - **Extraction d’entités médicales** : repère automatiquement les médicaments, posologies, symptômes, diagnostics, traitements, etc.  
	  - **Détection de relations** : par exemple, associer un médicament à une posologie ou à un symptôme.  
	  - **Protection de la confidentialité** : Identifie et anonymise les informations médicales personnelles (PHI) pour respecter HIPAA, etc.  
	  - **Service managé** : aucune infrastructure ML à gérer, service accessible via API.  
	  - **Intégration** : peut être couplé à S3 (pour stocker des documents), à Lambda pour automatiser le traitement, ou à d’autres solutions de stockage/analyse.

- **Cas d’usage :**  
	  - **Transformation de dossiers médicaux** : structurer et indexer automatiquement des rapports cliniques ou hospitaliers.  
	  - **Recherche clinique** : extraire rapidement les informations pertinentes pour des études.  
	  - **Amélioration du parcours patient** : automatiser la détection des antécédents, médicaments, diagnostics pour accélérer les tâches administratives.  
	  - **Conformité** : anonymisation partielle ou complète des documents avant leur partage.

- **Pour l’examen AWS SAA :**  
	  - Rarement abordé en détail, car c’est un service ML spécialisé, mais peut apparaître dans un scenario santé/PHI (HIPAA).  
	  - Comprehend Medical est, comme Comprehend, un service d’analyse de texte, mais spécialement entraîné sur le vocabulaire médical.

## Amazon SageMaker
Amazon SageMaker est une plateforme gérée qui facilite le développement, l’entraînement et le déploiement de modèles de machine learning à grande échelle. Il comprend différents modules pour préparer les données, entraîner les modèles (via des notebooks, des algos intégrés ou des frameworks open source comme TensorFlow, PyTorch, etc.) et déployer ces modèles en production.

- **Principales caractéristiques** :  
	  - **Environnement d’entraînement managé** : prend en charge des algorithmes intégrés (XGBoost, Random Cut Forest, etc.) ou des frameworks populaires (TensorFlow, PyTorch, MXNet…), en mode distribué si nécessaire.  
	  - **Studio et Notebooks** : environnement de développement complet, collaboratif et préconfiguré (SageMaker Studio).  
	  - **Processing Jobs** : permet de préparer et transformer des données à l’échelle, sans gérer soi-même des clusters.  
	  - **Hyperparameter Tuning** : optimisation automatique des hyperparamètres pour trouver les meilleures performances.  
	  - **Endpoints de déploiement managé** : simplifie la mise en production des modèles, avec auto scaling, A/B testing, logging, monitoring.  
	  - **SageMaker Pipelines** : orchestrations de flux ML (entraînement, test, déploiement) de manière reproductible et automatisée.  
	  - **Intégrations avancées** : avec IAM, VPC, S3, ECR, CloudWatch, Kubernetes (SageMaker Operators).

- **Cas d’usage** :  
	  - **Développement ML complet** : pipeline allant de l’exploration des données à la mise en production.  
	  - **Entraînements distribués** : entraînement de gros modèles sur des clusters d’instances GPU/CPU.  
	  - **MLOps** : industrialiser et automatiser le déploiement et la surveillance des modèles ML.

- **Pour l’examen AWS SAA** :  
	  - SageMaker est parfois cité quand on parle de ML/IA “sur mesure”, par opposition aux services pré-entraînés (Rekognition, Translate, Transcribe…).  
	  - SAA attend surtout que vous sachiez que c’est la plateforme ML managée de bout en bout sur AWS.

## Amazon Kendra
Amazon Kendra est un service de recherche intelligente entièrement managé, utilisant le machine learning pour fournir des résultats pertinents à partir de données diverses (documents, bases de connaissances, wikis, SharePoint, S3, etc.). Il indexe et comprend le contenu en langage naturel, simplifie la configuration de connecteurs vers de multiples sources (Microsoft 365, Salesforce, ServiceNow...) et propose une interface de recherche unifiée.  

- **Principales caractéristiques** :  
	  - Compréhension avancée du langage : capable d’interpréter les questions en langage naturel.  
	  - Connecteurs intégrés : facilite l’indexation de contenus dispersés (S3, SharePoint, bases de données).  
	  - Sécurité et contrôle d’accès : gère les autorisations pour présenter uniquement les résultats autorisés.  
	  - Suggestions automatiques : propose des requêtes pertinentes au fur et à mesure que l’utilisateur tape.  
	  - Requêtes analytiques : Kendra peut suggérer des extraits de réponse au lieu de simples liens.  
- **Cas d’usage :**  
	  - Recherche interne en entreprise : documents de formation, notes internes, FAQs, wikis…  
  	- Helpdesk et service client : permettre aux agents ou clients de trouver rapidement une réponse fiable.  
  	- Portails web : site de support ou de documentation, intégrant une barre de recherche intelligente.  
  	- Applications spécialisées : moteur de recherche personnalisé pour des archives volumineuses.  
- **Pour l’examen AWS SAA** : 
	- Généralement peu abordé en profondeur, mais Kendra est cité pour des scénarios de “recherche intelligente” sur documents complexes, différent de services plus simples comme CloudSearch.  

## Amazon Personalize
Amazon Personalize est un service de recommandation managé qui utilise le machine learning pour générer des suggestions personnalisées en temps quasi réel (produits, contenu, films, etc.). Il est inspiré des mêmes technologies de recommandation qu’Amazon.com. Voici l’essentiel :

- **Principales caractéristiques** :  
	  - **Personnalisation dynamique** : propose des recommandations ciblées pour chaque utilisateur, en se basant sur leurs interactions, préférences, historique, etc.  
	  - **Automatisation des modèles ML** : AWS se charge de former, d’optimiser et de déployer le modèle (choix d’algorithmes, hyperparamètres).  
	  - **Intégration aisée** : ingestion de données (S3, Kinesis, batch ou temps réel), endpoints de prédiction via API.  
	  - **Adaptation continue** : personnalisation évolue au fil des retours et interactions des utilisateurs.  
	  - **Cas d’usage** : suggestion de produits (e-commerce), recommandations de contenu (vidéo, articles), emails personnalisés, etc.

- **Cas d’usage** :  
	  - **E-commerce** : cross-selling, up-selling, liste “Articles que vous aimerez”.  
	  - **Plateformes de streaming** : recommandations de séries, films, musiques basées sur l’historique de visionnage.  
	  - **Media / news** : personnalisation de la page d’accueil (articles, vidéos).  
	  - **Marketing ciblé** : newsletters, promotions personnalisées, notifications push.  

- **Pour l’examen AWS SAA** :  
	  - Service de ML managé spécialisé dans la **recommandation**.  
	  - Concurrence / comparaison possible : SageMaker pour un ML plus custom, ou personnalisation logicielle “maison”.  

## Amazon Textract

- **Principales caractéristiques :**  
	  - **OCR avancé** : détecte le texte dans des documents scannés (PDF, images).  
	  - **Extraction des champs et des tables** : identifie automatiquement les colonnes, les cellules, et les champs de formulaire (noms, adresses, montants, etc.).  
	  - **Précision ML** : appréhende la mise en page, la typographie, et ne se limite pas aux simples blocs de texte.  
	  - **Amazon Augmented AI (A2I)** : possibilité de révision manuelle des données extraites pour plus de fiabilité.  
	  - **Scalabilité** : service serverless ; on peut traiter un grand volume de documents sans configurer de serveurs.  
	  - **Intégration** : couplage facile avec S3, Lambda, ou d’autres services pour automatiser des workflows (par exemple, ingestion de factures / formulaires).  

- **Cas d’usage :**  
	  - **Traitement automatique de factures et de reçus** : extraire montants, dates, fournisseurs.  
	  - **Dématérialisation de formulaires** : lire et interpréter les champs complétés à la main ou imprimés.  
	  - **Archivage et recherche** : indexer le contenu de documents PDF ou scannés pour pouvoir le retrouver par mots-clés (via, par exemple, Elasticsearch/OpenSearch).  
	  - **Secteur bancaire ou assurances** : automatiser la saisie de données depuis de la paperasse traditionnelle.  

- **Pour l’examen AWS SAA :**

	  - Peu évoqué en détail, mais peut apparaître dans des scénarios de **dématérialisation** ou d’**automatisation de flux documentaires**.  
	  - Concurrence/Comparaison :  
	    - **Rekognition** lit le texte basique sur des images, mais Textract est spécialisé dans la **lecture structurée (tables, formulaires)**.  

