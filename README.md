# 🛡️ SOC Lab — Supervision et détection avec Wazuh

Laboratoire personnel de cybersécurité consacré à la supervision des systèmes, à la collecte des événements de sécurité, à la détection des comportements suspects et à l'investigation d'alertes à l'aide de solutions SIEM telles que **Wazuh** et **Splunk**.

Ce projet me permet de mettre en pratique les méthodes et outils utilisés par un analyste SOC, à travers des scénarios de sécurité réalisés dans un environnement contrôlé.

Glossaire du laboratoire SOC : https://github.com/AAlkarshi/SOC-Labo-Wazuh/blob/main/documentation/GLOSSAIRE.md

## 🎯 Objectifs

- Déployer et administrer une infrastructure SIEM avec Wazuh.
- Connecter et superviser des endpoints Windows et Linux.
- Centraliser et analyser les journaux système et de sécurité.
- Détecter des événements suspects à partir des alertes Wazuh.
- Analyser les événements et leurs champs techniques.
- Réaliser le triage et l'investigation des événements.
- Documenter les résultats et formuler des recommandations.
- Exploiter le framework MITRE ATT&CK.
- Configurer la surveillance de l'intégrité des fichiers (FIM).
- Développer et tester des règles de détection personnalisées.
- Produire des rapports d'investigation structurés.

## 🏗️ Architecture du laboratoire

Le laboratoire repose sur un serveur Wazuh, une machine Ubuntu et un poste Windows 11 qui est une architecture centralisée autour de Wazuh. Les agents Wazuh transmettent les événements des machines supervisées au serveur, où ils sont analysés, indexés et consultables depuis le Dashboard.

Un environnement Splunk Enterprise est également utilisé dans le TP05 pour expérimenter la création de règles de détection personnalisées à partir des journaux Windows.

| Équipement | Système | Fonction |
|---|---|---|
| Serveur Wazuh | Linux | Manager, Indexer et Dashboard |
| Agent Linux | Ubuntu | Collecte des événements Linux et SSH |
| Agent Windows | Windows 11 | Collecte des événements Windows et Sysmon |
| Environnement Splunk | Windows 11 | Recherche SPL et création d'alertes personnalisées |

**Version de Wazuh :** 4.14.9

Pour plus de détails : [Architecture du laboratoire](./architecture/schema-laboratoire.md).

## 🔄 Fonctionnement de la supervision

Le fonctionnement du laboratoire reproduit une chaîne de traitement classique d'un SIEM :

1. Génération : une activité est réalisée sur un endpoint, dans le cadre d'une utilisation normale ou d'un scénario de test.
2. Collecte : les agents récupèrent les événements système et de sécurité.
3. Transmission : les événements sont envoyés à la plateforme SIEM concernée.
4. Analyse : les événements sont traités à l'aide de décodeurs, de règles et de recherches.
5. Détection : une alerte est générée lorsqu'une condition de détection est remplie.
6. Investigation : l'analyste examine les informations disponibles, le contexte et les indicateurs techniques.
7. Documentation : les résultats et recommandations sont notés dans un rapport.

## 🧰 Environnement technique

| Technologie | Utilisation |
|---|---|
| Wazuh SIEM | Collecte, supervision et détection |
| Wazuh Agent | Surveillance des endpoints |
| Wazuh Manager | Analyse et corrélation des événements |
| Wazuh Indexer | Stockage et indexation |
| Wazuh Dashboard | Recherche et visualisation des alertes |
| Splunk Enterprise | Recherche SPL et création d'alertes personnalisées |
| Sysmon | Collecte de télémétrie Windows |
| Ubuntu / Linux | Système supervisé et analyse des journaux |
| Windows 11 | Endpoint supervisé |
| OpenSSH | Tests et analyse des authentifications |
| Syscheck / FIM | Surveillance de l'intégrité des fichiers |
| MITRE ATT&CK | Classification des techniques d'attaque |
| VirtualBox | Virtualisation |
| Git / GitHub | Versionnement et documentation |

## 🔍 Travaux pratiques

Chaque TP présente un scénario, les manipulations réalisées, les événements collectés, les alertes obtenues et leur analyse.

| TP | Sujet | État |
|---|---|---|
| TP01 | Détection d'une authentification SSH échouée | ✅ Réalisé |
| TP02 | Analyse de tentatives répétées d'authentification SSH | ✅ Réalisé |
| TP03 | Investigation d'événements Windows avec Sysmon | ✅ Réalisé |
| TP04 | Surveillance de l'intégrité des fichiers (FIM) | ✅ Réalisé |
| TP05 | Création de règles de détection personnalisées avec Splunk | ✅ Réalisé  |
| TP06 | Bientôt |  🔄 En cours |

### TP01 — Détection d'une authentification SSH échouée

Simulation d'une tentative de connexion SSH avec un utilisateur inexistant, puis analyse de l'alerte générée par Wazuh.

- **Règle :** `5710`
- **Niveau d'alerte :** `5`
- **Agent concerné :** Ubuntu-VM
- **Adresse source :** `127.0.0.1`
- **Catégorie :** authentication_failed, invalid_login, sshd

**Compétences mises en pratique :** analyse des authentifications, identification d'un échec de connexion et lecture des champs d'une alerte.

[Consulter le TP01 et son rapport d'investigation](./TP01-authentification-ssh/README.md).

### TP02 — Analyse de tentatives répétées d'authentification SSH

Simulation de plusieurs tentatives de connexion SSH échouées depuis une machine distante, afin d'étudier les événements d'authentification et d'identifier un comportement pouvant correspondre à une attaque par force brute.

- **SIEM :** Wazuh
- **Règle observée :** 5710
- **Niveau d'alerte :** 5
- **Agent concerné :** Ubuntu-VM
- **Adresse IP source :** 192.168.1.103
- **MITRE ATT&CK :** T1110 – Brute Force

**Compétences mises en pratique :** analyse de tentatives répétées, identification de l'adresse source et étude des mécanismes de détection par fréquence.

[Consulter le TP02 et son rapport d'investigation.](https://github.com/AAlkarshi/SOC-Labo-Wazuh/blob/main/TP02-brute-force-ssh/README.md)

### TP03 — Investigation d'événements Windows avec Sysmon

Configuration de Sysmon sur Windows 11 et intégration de ses journaux dans Wazuh. Analyse d'un événement lié au lancement de l'utilitaire sdbinst.exe et investigation de son activité.

- **SIEM :** Wazuh
- **Source de données :** Microsoft-Windows-Sysmon/Operational
- **Règle observée :** 92058
- **Niveau d'alerte :** 12
- **Agent concerné :** Windows 11
- **Événement analysé :** lancement de sdbinst.exe
- **Investigation :** analyse du processus, vérification de sa signature numérique et examen de la clé de registre InstalledSDB.

**Compétences mises en pratique :** analyse de télémétrie Windows, investigation de processus, vérification d'indicateurs techniques et interprétation d'une alerte de niveau élevé.

[Consulter le TP03 et son rapport d'investigation.](https://github.com/AAlkarshi/SOC-Labo-Wazuh/blob/main/TP03-analyse-evenements-windows/README.md)

### TP04 — Surveillance de l'intégrité des fichiers (FIM)

Configuration du module File Integrity Monitoring (FIM) de Wazuh afin de surveiller les modifications apportées aux fichiers et répertoires sensibles d'un système Linux.

- **SIEM :** Wazuh
- **Module :** Syscheck / FIM
- **Agent concerné :** Ubuntu-VM
- **Répertoires surveillés :** /etc, /bin, /sbin et /boot
- **Mode de surveillance :** analyse périodique et surveillance en temps réel de /etc
- **Éléments analysés :** création, modification et suppression de fichiers, empreintes SHA-256 avant et après modification.

**Compétences mises en pratique :** configuration FIM, analyse des changements de fichiers, comparaison d'empreintes cryptographiques et investigation d'événements d'intégrité.

[Consulter le TP04 et son rapport d'investigation.](https://github.com/AAlkarshi/SOC-Labo-Wazuh/blob/main/TP04-DetectionModificationsFichiersFIM/README.md)

### TP05 — Détection des tentatives de connexion échouées Windows avec Splunk

Mise en place d'une détection personnalisée dans Splunk afin d'identifier les échecs d'authentification Windows et de générer des alertes à partir de l'Event ID 4625.

- **SIEM :** Splunk Enterprise
- **Source de données :** Windows Security
- **Événement surveillé :** Event ID 4625
- **Langage de recherche :** SPL
- **Identifiant de détection :** DET-WIN-001
- **Mécanisme :** SOC-Detection-Engine
- **MITRE ATT&CK :** T1110 – Brute Force
**Compétences mises en pratique :** collecte des journaux Windows, recherches SPL, création de règles de détection personnalisées, configuration d'alertes et vérification des événements générés.

[Consulter le TP05 et son rapport d'investigation.](https://github.com/AAlkarshi/SOC-Labo-Wazuh/blob/main/TP05-CreationdeReglePersonnalise/README.md)

## 🧠 Compétences mises en pratique

- Déploiement et administration de solutions SIEM (Wazuh et Splunk).
- Installation et supervision d'agents Linux et Windows.
- Collecte et analyse de journaux système et de sécurité.
- Triage et analyse d'alertes SIEM.
- Investigation des tentatives d'authentification échouées (SSH et Windows).
- Exploitation des événements Windows et Sysmon.
- Création de règles de détection personnalisées avec SPL.
- Surveillance de l'intégrité des fichiers (FIM).
- Utilisation de MITRE ATT&CK pour classifier les techniques d'attaque.
- Documentation technique et rédaction de rapports d'incident.

## 📈 Évolution du projet

Le laboratoire est enrichi progressivement par de nouveaux scénarios de détection, d'investigation et de supervision.

Les résultats, captures d'écran et rapports sont conservés dans les dossiers correspondant à chaque TP.

---

**Auteur :** Abdullrahman Al Karshi  
**Projet personnel — Blue Team / SOC**  

Laboratoire réalisé dans un environnement contrôlé à des fins d'apprentissage et de développement de compétences en cybersécurité défensive.
