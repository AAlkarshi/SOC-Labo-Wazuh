# TP05 – Détection des tentatives de connexion échouées Windows avec Splunk

## 1. Présentation du TP

Ce TP a pour objectif de mettre en place un mécanisme de détection personnalisé dans Splunk afin d'identifier des comportements suspects liés aux tentatives de connexion échouées sur un poste Windows 11.

L'exercice consiste à collecter les événements de sécurité Windows, à analyser les tentatives d'authentification échouées et à développer une règle de détection personnalisée permettant d'identifier un scénario de type Brute Force.

Un moteur de détection personnalisé (`SOC-Detection-Engine`) est utilisé pour analyser les événements et générer des alertes exploitables dans Splunk.

**Outils utilisés :**
- Splunk Enterprise
- Windows 11
- Journaux Windows Security
- SPL (Search Processing Language)
- SOC-Detection-Engine

## 2. Objectifs

- Collecter les événements de sécurité Windows dans Splunk.
- Identifier les tentatives de connexion échouées à partir de l'Event ID 4625.
- Comprendre et exploiter les événements d'authentification Windows.
- Concevoir une règle de détection personnalisée.
- Simuler des tentatives de connexion échouées dans un environnement de test.
- Vérifier le déclenchement de la règle de détection.
- Générer et indexer des alertes personnalisées dans Splunk.
- Exploiter les résultats pour réaliser une première analyse SOC.

## 3. Scénario de détection

Dans ce scénario, plusieurs tentatives de connexion échouées sont volontairement réalisées sur le poste Windows 11 afin de simuler un comportement pouvant correspondre à une attaque par force brute.

L'objectif est de vérifier si le moteur de détection personnalisé est capable d'identifier ce comportement et de générer une alerte.

**Événement Windows surveillé :**

| Événement | Description |
|---|---|
| Event ID 4625 | Échec d'une tentative d'ouverture de session Windows |
| Journal | Security |
| Source | WinEventLog:Security |

**Règle personnalisée :**

| Élément | Valeur |
|---|---|
| Identifiant | DET-WIN-001 |
| Nom | Détection Brute Force Windows |
| Moteur | SOC-Detection-Engine |
| Sourcetype de sortie | soc:alert:windows |
| Index de sortie | main |

## 4. Collecte et recherche des événements

Les événements Windows sont collectés et indexés dans Splunk.

Recherche SPL utilisée pour identifier les tentatives de connexion échouées :

```spl
index=ordilocalsystem sourcetype="WinEventLog:Security" EventCode=4625
| table _time host EventCode Account_Name Source_Network_Address
| sort - _time
```

Cette recherche permet de retrouver les événements 4625 et d'examiner les informations associées aux tentatives de connexion échouées.

## 5. Vérification des alertes générées

Les alertes produites par le moteur de détection personnalisé sont enregistrées dans l'index `main`, avec le sourcetype `soc:alert:windows`.

Recherche SPL utilisée :

```spl
index=main sourcetype="soc:alert:windows"
| stats count AS nombre_alertes by host source sourcetype
```

**Résultat observé lors du test :**

| Champ | Résultat |
|---|---|
| Host | Abdullrahman |
| Source | SOC-Detection-Engine |
| Sourcetype | soc:alert:windows |
| Nombre d'événements | 4 |

Une recherche complémentaire permet d'identifier les événements associés à la règle DET-WIN-001 :

```spl
index=main sourcetype="soc:alert:windows" "DET-WIN-001"
| table _time host source sourcetype _raw
| sort - _time
```

Cette recherche permet d'examiner les alertes générées et de retrouver leur contenu brut.

## 6. Analyse des résultats

Les recherches effectuées permettent de constater la présence d'événements Windows correspondant à des échecs d'authentification ainsi que des alertes personnalisées enregistrées dans Splunk.

Quatre événements ont été retrouvés dans l'index `main`, associés à la source `SOC-Detection-Engine`.

Une alerte portant l'identifiant `DET-WIN-001 - Détection Brute Force Windows` a également été observée.

Ces résultats constituent des éléments de preuve de la génération et de l'indexation des alertes. La validation complète de l'exécution de la règle repose également sur la vérification de son fonctionnement et de sa condition de déclenchement.

## 7. Compétences développées

- Collecte et exploitation des journaux de sécurité Windows.
- Analyse des événements d'authentification.
- Recherche et filtrage avec SPL.
- Compréhension des mécanismes de détection d'une attaque par force brute.
- Conception et validation de règles de détection personnalisées.
- Analyse des alertes de sécurité.
- Exploitation d'un SIEM : Splunk Enterprise.

## 8. Conclusion

Ce TP m'a permis de découvrir et de mettre en pratique les fonctionnalités de Splunk dans le cadre d'une activité de détection et de supervision de la sécurité.

J'ai travaillé sur la collecte des événements Windows, l'identification des échecs d'authentification et la génération d'alertes à partir d'une règle de détection personnalisée.

Ce laboratoire constitue une première mise en pratique des missions d'un analyste SOC : surveiller les événements, identifier les comportements suspects, vérifier les alertes et exploiter les données disponibles pour orienter une investigation.