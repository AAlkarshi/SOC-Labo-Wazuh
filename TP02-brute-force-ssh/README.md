# TP02 — Analyse de tentatives répétées d'authentification SSH

## 1. Objectif

Simuler plusieurs tentatives d'authentification SSH échouées depuis un poste Windows vers un serveur Ubuntu supervisé par Wazuh.

L'objectif est d'observer les événements générés par OpenSSH, de vérifier leur remontée dans le SIEM et d'analyser les alertes associées.

## 2. Environnement

| Équipement | Adresse IP | Rôle |
|---|---|---|
| Serveur Wazuh | 192.168.1.44 | Centralisation et analyse des alertes |
| Poste Windows 11 | 192.168.1.103 | Source des tentatives SSH |
| Agent Ubuntu | 192.168.1.79 | Cible des tentatives SSH |

## 3. Simulation

Depuis le poste Windows, plusieurs tentatives de connexion SSH ont été effectuées vers l'agent Ubuntu avec un nom d'utilisateur inexistant.

Le scénario a été réalisé dans un environnement de laboratoire contrôlé.

## 4. Détection et alertes Wazuh

Les événements générés par OpenSSH ont été collectés par l'agent Ubuntu puis transmis au serveur Wazuh.

<img width="1036" height="852" alt="image Force Brute" src="https://github.com/user-attachments/assets/06cf6d79-ed05-4c34-82c5-53d79fd9e7e8" />


Les alertes observées dans le Dashboard présentent les caractéristiques suivantes :

| Champ | Valeur observée |
|---|---|
| Agent | Ubuntu-VM |
| Règle Wazuh | 5710 |
| Description | Attempt to login using a non-existent user |
| Niveau | 5 |
| Utilisateur ciblé | utilisateur_inexistant |
| Adresse source attendue | 192.168.1.103 |
| Période observée | 16:52:09 à 16:52:27 |

Au moins 18 alertes sont visibles dans l'intervalle affiché.

## 5. Analyse

<img width="517" height="867" alt="image Détail" src="https://github.com/user-attachments/assets/ff1afffc-f7dc-40ac-acf8-a7e7603fbee7" />

La répétition des alertes sur une courte période montre une succession de tentatives d'authentification SSH échouées.

La règle 5710 identifie les tentatives utilisant un compte inexistant. Elle permet de détecter ce comportement, mais ne suffit pas à confirmer à elle seule une attaque par force brute.

Une vérification complémentaire du mécanisme de corrélation Wazuh est nécessaire pour qualifier précisément le scénario.

## 6. Conclusion provisoire

Le laboratoire démontre la bonne collecte des événements SSH par l'agent Ubuntu et leur détection par Wazuh.

Une série de tentatives d'authentification échouées a été observée depuis le poste Windows vers l'endpoint Ubuntu.

La détection d'une attaque par force brute au moyen d'une règle de corrélation reste à confirmer.

## 7. Recommandations

- Surveiller les tentatives d'authentification SSH répétées.
- Vérifier les adresses IP sources et les comptes ciblés.
- Mettre en place une politique de limitation des tentatives de connexion.
- Privilégier l'authentification par clé SSH.
- Examiner les règles de corrélation afin d'améliorer la détection des comportements répétitifs.
