# TP05 – Rapport d'investigation SOC
## Détection de tentatives de connexion échouées Windows avec Splunk

| Informations | Détails |
|---|---|
| Type d'incident simulé | Tentatives répétées de connexion échouées |
| Catégorie | Authentification / Brute Force |
| Environnement | Windows 11 |
| SIEM | Splunk Enterprise |
| Moteur de détection | SOC-Detection-Engine |
| Règle | DET-WIN-001 |
| Nom de la règle | Détection Brute Force Windows |
| Niveau de gravité | À confirmer selon la configuration |
| Statut | Test réalisé – validation technique à documenter |

---

## 1. Contexte de l'investigation

Dans le cadre de ce laboratoire SOC, une simulation de tentatives de connexion échouées a été réalisée sur un poste Windows 11.

L'objectif était de vérifier le bon fonctionnement d'une règle de détection personnalisée, conçue pour identifier un comportement pouvant correspondre à une attaque par force brute.

Les événements de sécurité Windows ont été collectés dans Splunk afin d'être analysés par le moteur de détection personnalisé `SOC-Detection-Engine`.

Cette activité est réalisée dans un environnement de test contrôlé. Les tentatives de connexion ont été volontairement générées pour vérifier le fonctionnement du dispositif de détection.

## 2. Description du scénario

Le scénario consiste à effectuer plusieurs tentatives d'authentification échouées sur le poste Windows.

Chaque échec d'authentification peut produire un événement de sécurité Windows identifié par le code 4625.

Le moteur de détection analyse les événements collectés afin de rechercher une répétition anormale des échecs d'authentification.

**Événement surveillé :**

| Champ | Description |
|---|---|
| Event ID | 4625 |
| LogName | Security |
| EventType | 0 |
| SourceName | Microsoft Windows security auditing |
| Signification | Échec d'ouverture de session |

## 3. Collecte des événements

Les événements ont été recherchés dans Splunk avec la requête suivante :

```spl
index=ordilocalsystem sourcetype="WinEventLog:Security" EventCode=4625
| table _time host EventCode Account_Name Source_Network_Address
| sort - _time
```

Cette recherche permet d'identifier les événements correspondant aux tentatives de connexion échouées.

### Observation

Les événements 4625 ont été retrouvés dans Splunk pendant la simulation.

Ils constituent la source de données utilisée pour tester le mécanisme de détection.

**Preuve associée :** capture des événements Windows 4625 dans Splunk.

## 4. Règle de détection personnalisée

Une règle de détection portant l'identifiant `DET-WIN-001` a été utilisée dans le moteur `SOC-Detection-Engine`.

| Paramètre | Valeur |
|---|---|
| Identifiant | DET-WIN-001 |
| Nom | Détection Brute Force Windows |
| Source | SOC-Detection-Engine |
| Type de sortie | soc:alert:windows |
| Index de destination | main |

L'objectif de cette règle est d'identifier une répétition de tentatives de connexion échouées pouvant indiquer un comportement de force brute.

**Point de contrôle :** la condition exacte de déclenchement, le seuil et la fenêtre temporelle doivent être documentés à partir de la configuration réelle de la règle.

## 5. Résultats de la détection

Les alertes générées par le moteur personnalisé ont été recherchées dans l'index `main`.

Requête SPL :

```spl
index=main sourcetype="soc:alert:windows"
| stats count AS nombre_alertes by host source sourcetype
```

### Résultats observés

| Champ | Valeur |
|---|---|
| Index | main |
| Host | Abdullrahman |
| Source | SOC-Detection-Engine |
| Sourcetype | soc:alert:windows |
| Nombre d'événements | 4 |

Une recherche complémentaire a permis de retrouver une alerte portant l'identifiant `DET-WIN-001`.

```spl
index=main sourcetype="soc:alert:windows" "DET-WIN-001"
| table _time host source sourcetype _raw
| sort - _time
```

### Analyse

La présence de quatre événements dans l'index confirme que des données correspondant au sourcetype `soc:alert:windows` ont été enregistrées.

La présence de l'identifiant `DET-WIN-001` dans les résultats permet également d'associer une alerte au mécanisme de détection personnalisé.

Cependant, le nombre d'événements indexés ne permet pas à lui seul de déterminer le nombre d'exécutions distinctes de la règle.

## 6. Analyse SOC

L'événement Windows 4625 est un indicateur pertinent pour surveiller les échecs d'authentification.

Une répétition de ces événements sur une période réduite peut révéler plusieurs situations :

- Tentatives répétées de connexion avec un mot de passe incorrect.
- Erreur de saisie répétée par un utilisateur légitime.
- Utilisation d'identifiants obsolètes par une application ou un service.
- Tentative d'accès non autorisée.
- Activité potentielle de force brute.

Dans ce laboratoire, les événements ont été générés volontairement pour tester la détection.

Il ne s'agit donc pas d'un incident de sécurité réel, mais d'un scénario de simulation.

## 7. Vérifications techniques complémentaires

Les contrôles suivants permettent de renforcer la validation de la règle :

| Vérification | Objectif | Statut |
|---|---|---|
| Collecte des événements 4625 | Vérifier la réception des événements Windows | Observé |
| Présence de DET-WIN-001 | Identifier l'alerte personnalisée | Observé |
| Indexation des alertes | Vérifier leur présence dans l'index main | Confirmé |
| Configuration de la règle | Vérifier le seuil et la fenêtre temporelle | À documenter |
| Historique d'exécution | Confirmer l'exécution effective de la règle | À documenter |
| Correspondance temporelle | Relier les événements sources à l'alerte | À vérifier |

## 8. Conclusion de l'investigation

Cette simulation a permis de mettre en pratique la collecte et l'analyse des événements de sécurité Windows dans Splunk Enterprise.

Les événements 4625 ont été identifiés dans l'index de collecte et des événements d'alerte associés au moteur `SOC-Detection-Engine` ont été retrouvés dans l'index `main`.

La présence de l'identifiant `DET-WIN-001` démontre qu'une alerte associée à la règle personnalisée a été produite et indexée.

La validation complète du fonctionnement de la règle nécessite toutefois de vérifier sa configuration, son historique d'exécution et la correspondance entre les événements Windows et l'alerte générée.

Ce TP m'a permis de développer mes compétences en recherche SPL, analyse des journaux Windows, compréhension des mécanismes de détection et exploitation des alertes dans un SIEM.

---

## 9. Éléments de preuve

Captures d'écran à joindre au rapport :

1. `01-collecte-evenements-4625.png` – Événements Windows collectés.
2. `02-regle-DET-WIN-001.png` – Configuration de la règle personnalisée.
3. `03-alerte-generee.png` – Alerte DET-WIN-001 retrouvée dans Splunk.
4. `04-analyse-alertes.png` – Résultat de la recherche statistique des alertes.

**Fin du rapport d'investigation – TP05**