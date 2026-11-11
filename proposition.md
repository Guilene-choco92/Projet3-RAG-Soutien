# Proposition — Projet 3

**Sujet :** Évaluation d’une RAG locale pour le soutien informatique  

**Membres :** Sèdrick Zahn, Abdoulaye Dembele, Catherine Guilene Ngo Ebga  
**Dépôt GitHub :** [Projet3-RAG-Soutien](https://github.com/Abdem80/Projet3-RAG-Soutien)  
**Invitation enseignant envoyée :** Oui — nicolas.payre@cegepsherbrooke.qc.ca  
**Date :** 10 novembre 2026

## La Technologie que l'on veut étudier

### Besoin et résultat attendu

**Demande :** un technicien de soutien informatique perd du temps à retrouver la bonne procédure (redémarrage d’un équipement réseau, diagnostic d’un service, résolution d’un incident connu) dans une documentation technique volumineuse, souvent rédigée en anglais.

**Utilisateur et administrateur :** le technicien de soutien (niveau 1 ou 2) interroge l’assistant. Un administrateur système installe le service, y charge la documentation et le maintient à jour.

**Résultat attendu :** un assistant documentaire local qui répond à une question en langage naturel en **citant ses sources** et qui indique clairement quand l’information est **absente** de la documentation. L’équipe déterminera aussi **quelle taille de modèle** et **quelle façon de construire le service** (script Python ou outil d’automatisation n8n) conviennent le mieux à ce besoin.

**Preuve d’utilité :** sur 20 questions dont la réponse est présente, l’assistant fournit une réponse correcte et cite la bonne source dans au moins 16 cas sur 20 (80 %), avec les seuils détaillés dans les tests. Sur les 5 questions sans réponse dans le corpus, il doit signaler l’absence d’information sans inventer (5 sur 5). Ces objectifs ne sont pas encore des résultats obtenus.

**Apport par rapport à l’exemple du cours (assistant documentaire) :**
- **Besoin nouveau :** soutien informatique avec une documentation bilingue (questions en français, documents en français et en anglais).
- **Expérimentations supplémentaires :** comparaison de deux modèles de génération, comparaison de deux façons d’intégrer le service (script Python et workflow n8n automatisé) et comparaison avec une recherche par mots-clés sans IA.
- **Analyse personnelle :** recommandation fondée sur nos mesures (qualité, ressources, facilité d’exploitation) pour un poste de soutien ordinaire.

### Technologie et alternative

**Technologie IA étudiée, version envisagée et rôle concret :** une RAG (génération augmentée par récupération) exécutée localement. Elle recherche des passages dans nos documents, puis utilise un modèle IA pour rédiger une réponse à partir de ces passages. Nous choisissons cette approche pour aider un technicien à retrouver une procédure tout en vérifiant la provenance des réponses.
- **Ollama** (dernière version stable au moment de l’installation, notée dans le README) : sert les modèles localement.
- **`bge-m3`** : modèle d’embeddings multilingue. Rôle : retrouver les passages pertinents, y compris pour une question en français portant sur un document en anglais.
- **`llama3.2:3b`** : petit modèle de génération. Rôle : formuler une réponse fondée uniquement sur les passages retrouvés.
- **`qwen2.5:7b`** : modèle de génération plus gros, comparé au précédent (à confirmer selon la mémoire disponible).
- **ChromaDB**, exécuté comme serveur : base vectorielle qui stocke les embeddings et renvoie les passages les plus proches. Les deux versions ci-dessous interrogent la même collection.

**Deux façons d’intégrer le même service :**
- **Version A — script Python :** un court script orchestre l’indexation, la recherche dans ChromaDB et l’appel au modèle.
- **Version B — n8n** (édition communautaire auto-hébergée, avec Docker) : la même chaîne est construite avec les nœuds IA de n8n (Ollama, Chroma Vector Store, chaîne question-réponse). La version B ajoute une automatisation : une question envoyée par formulaire web reçoit automatiquement une réponse avec ses sources.

**Alternative à comparer :**
- **Recherche par mots-clés (BM25) sans IA**, sur les mêmes documents et les mêmes questions (alternative principale, mesurée par essais).
- **Solution hébergée** (Cloudflare Workers AI + Vectorize), comparée à partir de la documentation seulement.

**Critères de comparaison et raisons de leur choix :**
- Pertinence des passages retrouvés et fidélité de la réponse aux sources : c’est la valeur du service pour le technicien.
- Traitement de l’information absente : une réponse inventée est dangereuse en dépannage.
- Temps de réponse et ressources utilisées (mémoire, processeur) : déterminent si le service est exploitable sur un poste ordinaire, et si un modèle plus gros vaut son coût en ressources.
- Coût : le projet doit rester gratuit, et une solution locale n’a pas de frais d’usage.
- Confidentialité des données : une solution locale garde la documentation sur le poste.
- Facilité d’installation, de modification et d’exploitation : compare le script Python et n8n du point de vue de l’administrateur.

**Agents IA utilisés pour nous aider :** ils servent à la recherche, à l’explication, au code et à la documentation. Ils sont distincts de la technologie étudiée. Leur usage est consigné brièvement dans `journal.md` (objectif, apport retenu, vérification effectuée, correction éventuelle), et toute information qu’ils fournissent est vérifiée dans les sources.

### Sources consultées et veille

| Titre et lien | Auteur ou organisme | Date de consultation | Fiabilité, information vérifiée et utilité |
|---|---|---|---|
| [Dépôt et guide officiels Ollama](https://github.com/ollama/ollama) | Ollama | 2026-11-10 | Source primaire maintenue par le projet. Le guide présente l’installation et l’exécution de modèles ; utile pour préparer le service local. Les versions et exigences seront validées à l’installation. |
| [Chroma — Getting Started](https://docs.trychroma.com/docs/overview/getting-started) | Chroma | 2026-11-10 | Documentation officielle : création de collections, ajout et recherche de documents. Utile pour l’indexation et la récupération. La compatibilité des versions avec n8n sera vérifiée en essai. |
| Documentation officielle : n8n, nœud Chroma Vector Store — https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.vectorstorechroma/ | n8n | 2026-11-10 | Source officielle. Information vérifiée : n8n possède un nœud Chroma Vector Store qui insère et récupère des documents, et qui se branche à une chaîne question-réponse. Confirme que la version B peut utiliser la même base que la version A. |

**Outil ou mécanisme de veille choisi :** suivi des *releases* des dépôts GitHub d’Ollama, de Chroma et de n8n (Watch > Custom > Releases), avec lecture des notes de version.

**Information à suivre pendant le projet et raison de ce choix :** les nouvelles versions et les changements pouvant affecter l’installation, l’API d’embeddings, les nœuds IA de n8n ou la disponibilité des modèles. Une mise à jour peut modifier les résultats des tests, donc il faut la repérer avant les essais et noter les versions utilisées.

La trace de suivi sera consignée dans le journal : date, information, source et conséquence pour le projet, même si elle confirme un choix sans le modifier.

## L'approche du projet

**Fonctions ou scénarios inclus, adaptés au nombre de participants (3) :**
- Indexation d’environ 25 documents techniques et constitution d’un jeu de 25 questions figé.
- Version A (script Python) et version B (workflow n8n avec formulaire automatisé) du même assistant.
- Comparaison de deux modèles de génération (`llama3.2:3b` et `qwen2.5:7b`).
- Comparaison avec la recherche BM25 et, sur documentation, avec Cloudflare.
- Trois tests répétés, analyse des résultats et recommandation.

**Ce qui dépasse le projet :** interface web élaborée, authentification, déploiement en production, grand volume de documents, entraînement ou ajustement fin d’un modèle.

**Réalisation envisagée : installation, configuration, administration, intégration ou programmation :** installation et configuration d’Ollama, de ChromaDB et de n8n ; intégration de ces composants par un court script Python (version A) et par un workflow n8n (version B) ; un script de tests qui produit les tableaux de résultats. Aucun développement lourd n’est prévu.

**Matériel ou machines virtuelles, logiciels et versions, connexions réseau, comptes et droits, coûts estimés et solution de repli :**
- **Matériel :** ordinateur personnel prévu pour les essais, avec un processeur Intel Core i7 au minimum et 16 Go de RAM. Le type de carte graphique reste à confirmer. Les mesures de temps et de mémoire seront réalisées sur ce même poste.
- **Logiciels :** Ollama, Python, ChromaDB, Docker et n8n (versions exactes notées dans le README).
- **Réseau :** Internet seulement pour télécharger les modèles, les images Docker et les paquets ; les essais se font ensuite en local. Connexions locales : n8n (conteneur Docker) → Ollama sur l’hôte (port 11434) et → serveur ChromaDB.
- **Comptes et droits :** aucun compte payant ; compte administrateur n8n local ; droits administrateur disponibles pour installer Ollama et Docker sur le poste d’essai. Aucun secret dans le dépôt.
- **Coûts estimés :** 0 $ de dépenses supplémentaires prévues, en utilisant le matériel existant et les éditions locales ; hors électricité et connexion Internet déjà disponibles.
- **Solutions de repli :** si la mémoire est insuffisante, envisager un modèle plus léger ou une réduction du périmètre à faire approuver par l’enseignant ; si le nœud Chroma de n8n pose problème, le magasin vectoriel intégré de n8n (Simple Vector Store, non persistant), après approbation si ce changement modifie le travail prévu, en le notant dans le rapport ; cette variante sera distinguée de la comparaison avec collection commune ; des résultats enregistrés pour la démonstration si un problème survient.

**Schéma simple ou tableau décrivant les relations entre ces éléments :**

```
                       ┌──────────── Poste d’essai ────────────┐
Technicien ──question──▶ Version A : script Python (terminal)   │
           ──formulaire─▶ Version B : n8n (conteneur Docker)    │
                       │        │                  │           │
                       │        ▼                  ▼           │
                       │  Ollama (bge-m3,     ChromaDB (serveur,│
                       │  llama3.2 / qwen2.5)  collection commune)
                       └────────────────────────────────────────┘
```

| Étape | Élément | Rôle |
|---|---|---|
| Indexation | Documents → découpage en passages → `bge-m3` (Ollama) → ChromaDB | Transformer les documents en vecteurs stockés |
| Question | Question (terminal en version A, formulaire en version B) → `bge-m3` → ChromaDB (k plus proches passages) | Retrouver les passages pertinents |
| Réponse | Passages + question → `llama3.2:3b` ou `qwen2.5:7b` (Ollama) → réponse et sources | Rédiger la réponse en citant les sources, ou indiquer que l’information est absente |
| Orchestration | Script Python (version A) ou workflow n8n (version B) | Enchaîner les étapes ci-dessus |

**Équipements ou raccordements nécessaires, s’il y a lieu :** aucun raccordement physique.

**Données utilisées, provenance et autorisation d’usage :** documents techniques fictifs rédigés par l’équipe (procédures de dépannage réseau et de services), en français et en anglais, ou documentation publique dont la licence permet l’usage. Aucune donnée confidentielle ou personnelle.

## Les grandes étapes du Projet

**Répartition initiale et preuves de contribution :** chaque membre a une partie autonome, testée et documentée à son nom. Les contributions sont visibles dans le dépôt (branche et pull requests par membre, relues par un autre membre), dans `journal.md` (entrées signées) et dans le rapport (section attribuée).

**Sèdrick Zahn — version A (script Python) et comparaison des modèles**

| Tâche | Preuve attendue |
|---|---|
| Installer Ollama et ChromaDB (serveur), télécharger `bge-m3`, `llama3.2:3b` et `qwen2.5:7b` | Procédure et versions dans le README |
| Écrire le script d’indexation (découpage en passages, embeddings, stockage dans ChromaDB) | Script dans le dépôt |
| Écrire le script de question-réponse (recherche, prompt strict, réponse avec sources, réponse « introuvable ») | Script et prompt dans le dépôt, capture d’une réponse |
| Exécuter les tests 1 à 3 sur la version A avec les deux modèles, trois fois chacun | Tableaux de résultats, mesures de temps et de mémoire |
| Rédiger la comparaison entre `llama3.2:3b` et `qwen2.5:7b` | Section du rapport à son nom |

**Abdoulaye Dembele — version B (n8n) et veille**

| Tâche | Preuve attendue |
|---|---|
| Installer n8n avec Docker et le connecter à Ollama et à ChromaDB | Procédure et versions dans le README, y compris la configuration réseau entre le conteneur et l’hôte |
| Construire le workflow RAG dans n8n (recherche dans ChromaDB, réponse avec sources) | Workflow exporté en JSON dans le dépôt, captures |
| Construire l’automatisation par formulaire (question envoyée → réponse automatique avec sources) | Workflow exporté, capture, présentation dans la démo |
| Ajouter un déclencheur Webhook pour les tests automatisés, puis exécuter les tests 1 et 2 sur la version B, trois fois | Tableaux de résultats de la version B |
| Rédiger la comparaison entre la version A et la version B | Section du rapport à son nom |
| Assurer la veille sur les *releases* d’Ollama, de Chroma et de n8n | Entrées datées dans `journal.md` avec la conséquence pour le projet |

**Catherine Guilene Ngo Ebga — corpus, tests et alternatives**

| Tâche | Preuve attendue |
|---|---|
| Rédiger le corpus d’environ 25 documents (français et anglais) | Documents dans le dépôt |
| Rédiger et figer le jeu de 25 questions avec les réponses et les sources attendues, avant les essais | Fichier de questions daté dans le dépôt |
| Écrire le script de tests qui interroge les versions A et B et produit les tableaux | Script dans le dépôt, tableaux générés |
| Mettre en place la recherche BM25 et l’exécuter sur les questions des tests 1 et 2 | Script et tableaux de résultats BM25 |
| Rédiger la comparaison avec BM25 et la comparaison documentaire avec Cloudflare | Section du rapport à son nom |

**Ensemble :** analyse des résultats, limites, recommandation (adoption, adoption sous conditions ou rejet), rapport et démonstration. Chaque membre présente sa partie et doit pouvoir expliquer ce que les essais permettent de conclure et ce qu’ils ne permettent pas d’affirmer.

| Cours | Résultat visé | Responsable(s) |
|---|---|---|
| 11 | Sujet approuvé et accès prêts : proposition remise dans Léa, dépôt GitHub créé, invitation envoyée | Toute l’équipe |
| 12 | Mise en œuvre, premier résultat et trace de veille : corpus et questions figés, version A fonctionnelle, n8n installé avec un premier workflow, BM25 prêt | Version A : Sèdrick · n8n et veille : Abdoulaye · corpus et BM25 : Catherine |
| 13 | Tests, comparaison et première recommandation : trois tests exécutés et répétés, comparaisons, limites | Chacun pour sa partie ; analyse en équipe |
| 14 | Démonstration et entretiens : démonstration de 10 minutes, preuves locales prêtes | Toute l’équipe |
| 15 | Remise finale : `remise.md` dans Léa ; `rapport.md`, `README.md`, `journal.md` et tag `projet3-final` dans le dépôt | Toute l’équipe |

Toute modification majeure du sujet ou du travail prévu sera soumise à l’enseignant pour approbation.

## Tests prévus

Les résultats attendus sont fixés **avant** les essais, à partir d’un jeu de 25 questions figé. La configuration de référence est la version A avec `llama3.2:3b`.

| Test | Conditions et scénario précis | Résultat attendu / seuil de réussite | Preuve à recueillir |
|---|---|---|---|
| 1 — Cas normal | 15 questions dont la réponse figure dans les documents, posées en français sur des documents en français | Le bon passage est dans les 3 premiers résultats, la réponse est correcte selon le corrigé et elle cite la bonne source, pour au moins 12 questions sur 15 | Tableau des résultats (CSV ou Markdown) et capture d’une réponse |
| 2 — Limite ou échec | 5 questions dont la réponse est absente des documents | L’assistant indique que l’information est introuvable, sans inventer, pour 5 questions sur 5 | Tableau des résultats et capture d’une réponse |
| 3 — Autre cas pertinent | 5 questions en français portant sur des documents en anglais, avec mesure du temps de réponse et de la mémoire utilisée | Au moins 4 réponses sur 5 correctes et citées ; temps et mémoire consignés (seuil de temps fixé au cours 12, avant le test) | Tableau des résultats, mesures de temps et de mémoire, capture |

Les tests vérifient le service IA étudié et son utilité : qualité des résultats, traitement de l’information absente, ressources et usage multilingue.

**Comparaison prévue :**
- **BM25 (essais) :** la recherche par mots-clés est exécutée sur les mêmes 25 questions que les tests 1 à 3. On compare la présence du passage attendu dans les 3 premiers résultats pour les 20 questions avec réponse. Pour les 5 questions sans réponse, on observe les passages non pertinents retournés ; BM25 seul ne rédige pas de réponse et son absence de refus ne sera pas évaluée comme celle d’un assistant.
- **Modèles (essais) :** les tests 1 à 3 sont exécutés avec `llama3.2:3b`, puis avec `qwen2.5:7b`. On compare le taux de réussite, le temps de réponse et la mémoire utilisée.
- **Version A et version B (essais) :** les tests 1 et 2 sont exécutés sur le workflow n8n avec les mêmes questions. On compare les résultats, le temps de réponse, ainsi que le temps et les difficultés de mise en place et de modification.
- **Cloudflare (documentation) :** comparaison selon les mêmes critères à partir de la documentation officielle, en distinguant ce qui est documenté de ce qui est mesuré.

**Répétitions :** chaque test sera répété trois fois avec les mêmes paramètres, car la sortie d’un LLM peut varier. Le modèle, sa version et ses paramètres (par exemple la température) seront notés. Les résultats seront analysés par le taux moyen de réussite et l’écart entre les répétitions.

**Comparaison des solutions :** pour comparer les solutions de façon équitable, nous utiliserons les mêmes documents, les mêmes questions et le même ordinateur. Les réponses seront vérifiées à l’aide des réponses et des sources attendues.
