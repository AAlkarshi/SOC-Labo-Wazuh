# 🛡️ SOC Lab — Supervision et détection avec Wazuh

Laboratoire personnel de cybersécurité consacré à la supervision des systèmes, à la détection d'événements de sécurité et à l'analyse d'alertes avec **Wazuh SIEM**.

Ce projet me permet de mettre en pratique les méthodes et outils utilisés par un analyste SOC, à travers des scénarios de sécurité réalisés dans un environnement contrôlé.

Glossaire du laboratoire SOC : https://github.com/AAlkarshi/SOC-Labo-Wazuh/blob/main/documentation/GLOSSAIRE.md

## 🎯 Objectifs

- Déployer et administrer une plateforme SIEM.
- Superviser des endpoints Linux et Windows.
- Centraliser et analyser les journaux système et de sécurité.
- Détecter des événements suspects à partir des alertes Wazuh.
- Réaliser le triage et l'investigation des événements.
- Documenter les résultats et formuler des recommandations.

## 🏗️ Architecture du laboratoire

Le laboratoire repose sur un serveur Wazuh, une machine Ubuntu et un poste Windows 11.

| Équipement | Système | Fonction |
|---|---|---|
| Serveur Wazuh | Linux | Manager, Indexer et Dashboard |
| Agent Linux | Ubuntu | Collecte des événements Linux et SSH |
| Agent Windows | Windows 11 | Collecte des événements Windows et Sysmon |

**Version de Wazuh :** 4.14.9

Pour plus de détails : [Architecture du laboratoire](./architecture/schema-laboratoire.md).

## 🧰 Environnement technique

| Technologie | Utilisation |
|---|---|
| Wazuh SIEM | Collecte, supervision et détection |
| Wazuh Agent | Surveillance des endpoints |
| Sysmon | Collecte de télémétrie Windows |
| Ubuntu / Linux | Système supervisé et analyse des journaux |
| Windows 11 | Endpoint supervisé |
| OpenSSH | Tests et analyse des authentifications |
| VirtualBox | Virtualisation |
| Git / GitHub | Versionnement et documentation |

## 🔍 Travaux pratiques

Chaque TP présente un scénario, les manipulations réalisées, les événements collectés, les alertes obtenues et leur analyse.

| TP | Sujet | État |
|---|---|---|
| TP01 | Détection d'une authentification SSH échouée | ✅ Réalisé |
| TP02 | Analyse de tentatives répétées d'authentification SSH | ✅ Réalisé |
| TP03 | Investigation d'événements Windows avec Sysmon | 🔄 En cours |
| TP04 | Surveillance de l'intégrité des fichiers (FIM) | À venir |
| TP05 | Création de règles de détection personnalisées | À venir |

### TP01 — Détection d'une authentification SSH échouée

Simulation d'une tentative de connexion SSH avec un utilisateur inexistant, puis analyse de l'alerte générée par Wazuh.

- **Règle :** `5710`
- **Niveau d'alerte :** `5`
- **Agent concerné :** Ubuntu-VM
- **Adresse source :** `127.0.0.1`

[Consulter le TP01 et son rapport d'investigation](./TP01-authentification-ssh/README.md).

## 🧠 Compétences mises en pratique

- Déploiement et administration d'une plateforme Wazuh.
- Installation et supervision d'agents Linux et Windows.
- Collecte et analyse de journaux système.
- Triage et analyse d'alertes SIEM.
- Investigation de tentatives d'authentification SSH.
- Exploitation des événements Sysmon.
- Documentation technique et rédaction de rapports d'incident.

## 📈 Évolution du projet

Le laboratoire est enrichi progressivement par de nouveaux scénarios de détection, d'investigation et de supervision.

Les résultats, captures d'écran et rapports sont conservés dans les dossiers correspondant à chaque TP.

---

**Auteur :** Abdullrahman Al Karshi  
**Projet personnel — Blue Team / SOC**  

Laboratoire réalisé dans un environnement contrôlé à des fins d'apprentissage et de développement de compétences en cybersécurité défensive.
