# TP05 – Rapport d'investigation SOC
## Création et validation d'une règle de détection Brute Force Windows avec Splunk

| Informations | Détails |
|---|---|
| Type de scénario | Tentatives répétées de connexion échouées |
| Catégorie | Authentification / Brute Force |
| Environnement | Windows 11 |
| SIEM | Splunk Enterprise |
| Moteur de détection | SOC-Detection-Engine |
| Identifiant de règle | DET-WIN-001 |
| Nom de la règle | Détection Brute Force Windows |
| Statut | Simulation réalisée – preuves de détection observées |

---

## 1. Contexte de l'investigation

Dans le cadre de ce laboratoire SOC, une règle de détection personnalisée a été conçue afin d'identifier des comportements pouvant correspondre à une attaque par force brute sur un poste Windows 11.

L'objectif est de mettre en pratique les différentes étapes du processus de détection :

- Identification des événements de sécurité à surveiller.
- Définition d'une logique de détection.
- Création et configuration d'une règle personnalisée.
- Simulation d'un comportement suspect.
- Vérification de l'exécution de la règle.
- Analyse des alertes générées dans Splunk.

L'ensemble des manipulations a été réalisé dans un environnement de test contrôlé. Les tentatives de connexion ont été volontairement générées pour valider le dispositif de détection.

## 2. Description du scénario

Le scénario consiste à réaliser plusieurs tentatives d'authentification échouées sur un poste Windows 11.

Chaque échec d'authentification peut générer un événement Windows identifié par le code 4625.

Une répétition de ces événements sur une période donnée peut révéler un comportement suspect et potentiellement correspondre à une attaque par force brute.

**Événement surveillé :**

| Champ | Description |
|---|---|
| Event ID | 4625 |
| LogName | Security |
| EventType | 0 |
| SourceName | Microsoft Windows security auditing |
| Signification | Échec d'ouverture de session |

## 3. Collecte et identification des événements

Avant de créer la règle, il est nécessaire d'identifier les événements disponibles dans Splunk.

La recherche suivante permet de retrouver les tentatives de connexion échouées :

```spl
index=ordilocalsystem sourcetype="WinEventLog:Security" EventCode=4625
| table _time host EventCode Account_Name Source_Network_Address
| sort - _time
```

### Observation

Les événements 4625 ont été retrouvés dans Splunk pendant la simulation.

Ils constituent la source de données utilisée pour concevoir et tester la règle de détection.

**Analyse :**

L'Event ID 4625 est pertinent pour surveiller les échecs d'authentification. Toutefois, un événement isolé ne suffit pas à caractériser une attaque par force brute.

La répétition des échecs et leur fréquence doivent être prises en compte dans la logique de détection.

## 4. Conception et création de la règle personnalisée

### 4.1 Définition du besoin

À partir des événements collectés, le besoin de détection a été défini : identifier une répétition anormale de tentatives de connexion échouées sur le système Windows.

La règle doit permettre de distinguer un simple échec d'authentification d'un comportement pouvant correspondre à une tentative de force brute.

### 4.2 Identification de la règle

Une règle personnalisée a été créée dans le moteur `SOC-Detection-Engine`.

<img width="1272" height="297" alt="TP05_CréationRegleAlerte" src="https://github.com/user-attachments/assets/f69ce8a2-2b9f-4564-94cb-5aed637f9135" />

| Paramètre | Valeur |
|---|---|
| Identifiant | DET-WIN-001 |
| Nom | Détection Brute Force Windows |
| Moteur | SOC-Detection-Engine |
| Événement surveillé | Windows Event ID 4625 |
| Sourcetype de sortie | soc:alert:windows |
| Index de destination | main |

### 4.3 Logique de détection

La logique repose sur l'analyse des événements d'échec d'authentification Windows.

Le principe consiste à rechercher une répétition des événements 4625 répondant aux conditions définies dans la règle.

Les paramètres à documenter à partir de la configuration réelle sont :

| Paramètre | Configuration |
|---|---|
| Événement surveillé | Event ID 4625 |
| Seuil de déclenchement | À renseigner |
| Fenêtre temporelle | À renseigner |
| Conditions de regroupement | À renseigner |
| Identifiant de règle | DET-WIN-001 |

Ces paramètres permettent de définir à partir de quel comportement la règle doit générer une alerte.

### 4.4 Création et configuration

La création de la règle a consisté à :

1. Identifier l'événement Windows à surveiller.
2. Définir le comportement suspect recherché.
3. Attribuer un identifiant unique à la règle : `DET-WIN-001`.
4. Définir son nom : `Détection Brute Force Windows`.
5. Configurer ses conditions de déclenchement.
6. Prévoir la génération d'une alerte exploitable dans Splunk.

<img width="700" height="751" alt="TP05_DetailAlerte1" src="https://github.com/user-attachments/assets/a2dbe9b1-1443-4e97-a226-653a5423c0d6" />
<img width="697" height="612" alt="TP05_DetailAlerte2" src="https://github.com/user-attachments/assets/1fffdd22-ccba-4a2a-a65a-ae4df27ea40d" />

## 5. Simulation des tentatives de connexion

Une simulation de tentatives de connexion échouées a été réalisée sur le poste Windows 11.

Cette étape permet de générer des événements 4625 afin de tester le comportement du mécanisme de détection.

L'objectif est de vérifier que les événements collectés correspondent aux critères définis dans la règle `DET-WIN-001`.

Cette simulation ne constitue pas une attaque réelle : elle est réalisée volontairement dans le cadre du laboratoire.

## 6. Vérification de la détection et des alertes

Les alertes produites par le moteur personnalisé ont été recherchées dans l'index `main`.

**Recherche SPL :**

```spl
index=main sourcetype="soc:alert:windows"
| stats count AS nombre_alertes by host source sourcetype
```

### 6.1 Résultats observés

<img width="1145" height="386" alt="TP05_AlerteDetecte" src="https://github.com/user-attachments/assets/ecfac19a-8391-4ca7-a677-7bbefebe3e2f" />

| Champ | Valeur |
|---|---|
| Index | main |
| Host | Abdullrahman |
| Source | SOC-Detection-Engine |
| Sourcetype | soc:alert:windows |
| Nombre d'événements | 4 |

Quatre événements correspondant au sourcetype `soc:alert:windows` ont été retrouvés dans l'index.

### 6.2 Recherche de l'alerte personnalisée

Pour retrouver les événements associés à la règle :

```spl
index=main sourcetype="soc:alert:windows" "DET-WIN-001"
| table _time host source sourcetype _raw
| sort - _time
```

<img width="770" height="345" alt="TP05_DetailAlerteTrouve" src="https://github.com/user-attachments/assets/082cb1b2-3968-4d72-b824-c0a3da1438e6" />

Une alerte portant l'identifiant `DET-WIN-001 - Détection Brute Force Windows` a été observée.

### 6.3 Analyse

La présence de l'identifiant de la règle dans les événements d'alerte permet d'établir un lien entre le moteur personnalisé et les alertes enregistrées dans Splunk.

Les quatre événements observés démontrent que des données d'alerte ont été indexées.

Il convient cependant de distinguer le nombre d'événements enregistrés du nombre d'exécutions distinctes de la règle.

Pour confirmer précisément son exécution, il faut également rapprocher l'historique du moteur, les événements sources et les horodatages des alertes.

## 7. Analyse SOC

L'événement Windows 4625 constitue un indicateur intéressant pour surveiller les tentatives d'authentification échouées.

Plusieurs situations peuvent expliquer leur répétition :

- Erreurs répétées de saisie de mot de passe.
- Utilisation d'identifiants obsolètes par une application.
- Tentatives d'accès non autorisées.
- Tentative potentielle de force brute.

Dans ce laboratoire, les événements ont été volontairement générés pour tester la règle personnalisée.

Aucun incident réel n'est donc revendiqué.

L'alerte doit être interprétée en tenant compte du contexte, de la fréquence des événements et des informations disponibles sur le compte et la machine concernés.

## 8. Vérifications techniques complémentaires

| Vérification | Objectif | Résultat |
|---|---|---|
| Collecte des événements 4625 | Vérifier la réception des événements Windows | Observé |
| Création de DET-WIN-001 | Vérifier l'existence de la règle personnalisée | Observé |
| Présence de l'identifiant dans les alertes | Associer les alertes à la règle | Observé |
| Indexation des alertes | Vérifier leur présence dans l'index main | Confirmé |
| Seuil et fenêtre temporelle | Vérifier les paramètres réels de détection | À documenter |
| Historique d'exécution | Confirmer les exécutions de la règle | À vérifier |
| Correspondance temporelle | Relier les événements sources aux alertes | À vérifier |

## 9. Éléments de preuve

Les captures suivantes permettent d'illustrer les différentes étapes du TP :

1. `TP05_CréationRegleAlerte.png` – Création et configuration de la règle personnalisée.
2. `TP05_AlerteDetecte.png` – Détection de l'activité simulée.
3. `TP05_DetailAlerte1.png` – Détails de la première alerte.
4. `TP05_DetailAlerte2.png` – Détails complémentaires de l'alerte.
5. `TP05_DetailAlerteTrouve.png` – Recherche et identification de l'alerte dans Splunk.

## 10. Conclusion de l'investigation

Ce TP a permis de mettre en pratique la conception et la création d'une règle de détection personnalisée dans Splunk Enterprise.

À partir des événements Windows 4625, une logique de détection des tentatives répétées de connexion échouées a été définie et associée à la règle `DET-WIN-001`.

Une simulation a ensuite été réalisée afin de tester le mécanisme de détection.

Les résultats obtenus montrent la présence d'événements d'alerte dans l'index `main`, associés au moteur `SOC-Detection-Engine`, ainsi qu'une alerte identifiée par `DET-WIN-001`.

Cette expérience m'a permis de développer mes compétences en création de règles de détection, exploitation de journaux Windows, recherche SPL et analyse des alertes dans un environnement SIEM.

---

**Fin du rapport d'investigation – TP05**
