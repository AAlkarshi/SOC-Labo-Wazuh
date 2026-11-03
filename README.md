# 🛡️ SOC Lab — Supervision et détection avec Wazuh

Laboratoire personnel de cybersécurité consacré à la supervision des systèmes, à l'analyse des journaux et à la détection d'événements de sécurité avec **Wazuh SIEM**.

L'objectif est de développer des compétences pratiques dans le domaine du **SOC (Security Operations Center)** à travers des scénarios de sécurité contrôlés, leur détection et l'analyse des alertes générées.

## 🎯 Objectifs du projet

- Déployer et administrer une plateforme SIEM avec Wazuh.
- Connecter et superviser des postes Linux et Windows.
- Collecter et analyser les journaux système et de sécurité.
- Simuler des événements de sécurité dans un environnement contrôlé.
- Identifier et analyser les alertes générées par Wazuh.
- Documenter les investigations et les résultats.

## 🏗️ Architecture du laboratoire

Le laboratoire repose sur une infrastructure virtualisée et un poste Windows physique.

| Équipement | Système | Rôle | Adresse IP |
|---|---|---|---|
| Serveur Wazuh | Linux | SIEM : Manager, Indexer, Dashboard | `192.168.1.44` |
| Agent Linux | Ubuntu | Supervision et génération d'événements SSH | `192.168.1.79` |
| Agent Windows | Windows 11 | Supervision système et collecte Sysmon | `192.168.1.103` |

**Version de Wazuh :** 4.14.9

## 🧰 Technologies utilisées

| Outil | Utilisation |
|---|---|
| Wazuh | SIEM, collecte et détection des événements |
| Wazuh Agent | Supervision des endpoints |
| Sysmon | Collecte de télémétrie avancée sous Windows |
| Ubuntu | Système Linux supervisé |
| Windows 11 | Poste Windows supervisé |
| VirtualBox | Virtualisation du laboratoire |
| SSH / OpenSSH | Génération et analyse d'événements d'authentification |
| Git / GitHub | Versionnement et documentation du projet |

## 🔍 Travaux pratiques

Les scénarios sont documentés individuellement. Chaque dossier présente le contexte, la procédure de test, les alertes observées et leur analyse.

| TP | Sujet | État |
|---|---|---|
| TP01 | Détection d'une authentification SSH échouée | ✅ Réalisé |
| TP02 | Analyse de tentatives répétées d'authentification SSH | 🔄 En cours |
| TP03 | Analyse des événements Windows avec Sysmon | À venir |
| TP04 | Surveillance de l'intégrité des fichiers (FIM) | À venir |
| TP05 | Création de règles de détection personnalisées | À venir |

### TP01 — Authentification SSH échouée

Détection d'une tentative de connexion SSH avec un utilisateur inexistant.

- Règle Wazuh : `5710`
- Niveau d'alerte : `5`
- Équipement supervisé : Ubuntu
- Source du test : `127.0.0.1`

➡️ [Consulter le TP01 et son rapport détaillé](./TP01-authentification-ssh/README.md)

## 🧠 Compétences travaillées

- Administration et supervision d'un environnement Linux.
- Déploiement et gestion d'agents Wazuh.
- Collecte et analyse de journaux de sécurité.
- Analyse d'alertes SIEM.
- Investigation de tentatives d'authentification suspectes.
- Utilisation de Sysmon pour la visibilité des événements Windows.
- Documentation technique et rédaction de rapports d'incident.

## 📌 Évolution du laboratoire

Ce projet évolue progressivement avec de nouveaux scénarios de détection et d'investigation. Les résultats sont documentés au fur et à mesure de leur réalisation.

---

# 🛠️ Compétences démontrées

- Wazuh SIEM
- Déploiement d'agents
- Analyse de logs Linux
- Analyse des authentifications SSH
- Triage d'alertes
- Investigation d'événements
- Analyse d'adresse IP
- MITRE ATT&CK
- Documentation d'incidents
- Linux & Windows

---

## 📌 Auteur

**Abdullrahman Al Karshi**

Junior SOC Analyst — Cybersecurity Lab


**Projet personnel réalisé dans un environnement de laboratoire contrôlé, à des fins d'apprentissage et de développement de compétences en cybersécurité défensive (Blue Team / SOC).**
