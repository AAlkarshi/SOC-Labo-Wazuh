# SOC-Labo-Wazuh
Laboratoire pratique SOC : détection et analyse d'événements de sécurité avec Wazuh

## 🎯 Objectif

L'objectif de ce laboratoire est de mettre en place une infrastructure de supervision avec Wazuh et d'analyser une tentative d'authentification SSH échouée sur une machine Ubuntu.

Ce projet me permet de démontrer mes compétences en :

- Déploiement et utilisation d'un SIEM
- Installation et configuration d'agents Wazuh
- Collecte et centralisation des journaux
- Analyse d'événements de sécurité
- Investigation d'alertes
- Analyse des journaux Linux
- Utilisation de MITRE ATT&CK
- Rédaction d'un rapport d'incident

---

# 🖥️ Architecture du laboratoire

```text
┌─────────────────────────┐
│      Windows 11         │
│                         │
│   Wazuh Agent — 001     │
│   IP : 192.168.1.103    │
└────────────┬────────────┘
             │
             │ Logs / événements
             ▼
┌─────────────────────────┐
│       Wazuh SIEM        │
│                         │
│ Manager + Dashboard     │
│ IP : 192.168.1.44       │
│ Version : 4.14.9        │
└────────────▲────────────┘
             │
             │ Logs / alertes
             │
┌────────────┴────────────┐
│      Ubuntu 26.04.1     │
│                         │
│   Wazuh Agent — 002     │
│   Nom : Ubuntu-VM       │
│   IP : 10.0.2.15        │
└─────────────────────────┘
```

---

# 🔧 Environnement technique

| Composant              | Technologie        |
| ---------------------- | ------------------ |
| SIEM                   | Wazuh 4.14.9       |
| Serveur de supervision | Wazuh Manager      |
| Tableau de bord        | Wazuh Dashboard    |
| Système Linux          | Ubuntu 26.04.1 LTS |
| Système Windows        | Windows 11 Home    |
| Serveur SSH            | OpenSSH            |
| Virtualisation         | VirtualBox         |

---

# ⚙️ Mise en place

## Installation de l'agent Ubuntu

L'agent Wazuh a été installé sur la machine Ubuntu et configuré pour communiquer avec le serveur Wazuh.

Configuration :

- Nom de l'agent : `Ubuntu-VM`
- Agent ID : `002`
- Adresse IP de l'agent : `10.0.2.15`
- Version Wazuh : `4.14.9`

  <img width="1591" height="487" alt="image Adresse IP Ubuntu VM" src="https://github.com/user-attachments/assets/c3500976-0c2c-4292-9d5a-36c5b3c0e400" />


Après l'installation, le service a été activé et démarré :

```bash
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent

```

L'agent est ensuite apparu dans le tableau de bord Wazuh avec le statut : **Active**

<img width="942" height="442" alt="image Wazuh Agents" src="https://github.com/user-attachments/assets/e2101cb6-ccbf-4c59-b812-a2fd8ee009db" />

---

# 🔍 Scénario de sécurité

Une tentative d'authentification SSH a été volontairement générée à des fins de laboratoire.

<img width="1146" height="67" alt="image repéré sur Wazuh (4 tentatives)" src="https://github.com/user-attachments/assets/e4ba1d13-c539-481d-bcdb-5c3635328881" />


Celui-ci consistait à tenter une connexion SSH avec un utilisateur inexistant :

```text
user_inexistant
```

Plusieurs tentatives ont été générées afin de vérifier la capacité de Wazuh à détecter et analyser les événements d'authentification SSH échoués.

---

# 🚨 Alerte détectée

Wazuh a généré une alerte correspondant à la règle :

<img width="587" height="847" alt="Alerte_Detail_Wazuh_Tentative" src="https://github.com/user-attachments/assets/5912df29-d02f-4727-82f6-b2102115f919" />


Détails importants :

| Élément           | Valeur                                             |
| ----------------- | -------------------------------------------------- |
| Rule ID           | `5710`                                             |
| Niveau de gravité | `5/15`                                             |
| Description       | `sshd: Attempt to login using a non-existent user` |
| Agent             | `Ubuntu-VM`                                        |
| Agent ID          | `002`                                              |
| Adresse source    | `127.0.0.1`                                        |
| Utilisateur       | `user_inexistant`                                  |

---

# 📄 Analyse du journal

Le journal complet analysé était :

```text
Nov 02 15:10:19 UbuntuUdemy sshd-session[23816]: Failed password for invalid user user_inexistant from 127.0.0.1 port 45234 ssh2
```

## Informations extraites

| Information       | Valeur                   |
| ----------------- | ------------------------ |
| Date              | 02 novembre 2026         |
| Heure             | 15:10:19                 |
| Machine           | UbuntuUdemy              |
| Service           | sshd-session             |
| Utilisateur       | user_inexistant          |
| Type d'événement  | Échec d'authentification |
| Adresse IP source | 127.0.0.1                |
| Port source       | 45234                    |
| Protocole         | SSH2                     |

---

# 🧠 Analyse SOC

L'événement indique une tentative d'authentification SSH utilisant un utilisateur inexistant.

La règle Wazuh `5710` a identifié cet événement comme une tentative de connexion utilisant un compte inexistant et son niveau de gravité est de niveau 5/15(Moyen) avec comme système concerné Ubuntu-VM avec comme L'adresse IP `127.0.0.1` qui correspond à l'adresse de bouclage (localhost). Cela indique donc que cette tentative provient localement de la machine Ubuntu et non d'un système distant. C'est une tentative locale mis au point par mes soins qui cible ici un compte "user_inexistant" qui a eu comme résultat une authentification qui à échouée.

Le champ `firedtimes` indiquait une valeur de `4`, ce qui signifie que la règle avait été déclenchée à 4 reprises pour des événements correspondants.

L'authentification a échoué et aucune connexion réussie n'est indiquée dans cet événement.

<img width="830" height="361" alt="image détail règle 5710" src="https://github.com/user-attachments/assets/e4cb8263-0bbb-4460-b598-f520600b28d1" />


---

# 🎯 Mapping MITRE ATT&CK

<img width="587" height="211" alt="Detail MITRE ATTACK" src="https://github.com/user-attachments/assets/ded42b93-819a-4778-b175-600f1bc90263" />


Wazuh a associé l'événement aux techniques suivantes :

## T1110.001 — Password Guessing

**Tactique : Credential Access**

Cette technique peut être associée à des tentatives répétées d'authentification utilisant différents MDP ou comptes.

## T1021.004 — SSH

**Tactique : Lateral Movement**

Cette technique concerne l'utilisation du protocole SSH pour accéder à un système.

> Les associations MITRE ATT&CK fournies par Wazuh permettent de contextualiser l'événement. Ce ne sont pas une preuve qu'une attaque réelle a eu lieu.

---

# 🔎 Investigation

Les éléments suivants ont été analysés :

- Adresse IP source
- Utilisateur utilisé
- Service concerné
- Résultat de l'authentification
- Nombre d'occurrences de la règle
- Contexte de la machine

L'adresse `127.0.0.1` a permis d'identifier une origine locale et qui est mise en place par moi-même.

Dans le cadre de ce laboratoire, les événements ont été volontairement générés afin de tester la détection et la centralisation des événements par Wazuh.

---

# 📋 Rapport d'incident SOC

| Élément | Description |
|---|---|
| Référence | SOC-TP01-2026-001 |
| Type d'événement | Authentification SSH échouée |
| Source de détection | Wazuh SIEM |
| Règle | 5710 |
| Gravité Wazuh | 5/15 |
| Machine concernée | Ubuntu-VM |
| Adresse source | 127.0.0.1 |
| Utilisateur | user_inexistant |
| Statut | Analyse terminée |
| Classification | Simulation de laboratoire |

## Évaluation de l'incident

**Impact :** aucun accès non autorisé identifié.

**Origine :** tentative générée localement dans le cadre
d'un exercice contrôlé.

**Résultat :** authentification échouée.

## Actions réalisées

- Identification de l'alerte dans Wazuh.
- Analyse du journal SSH.
- Identification de l'adresse IP source.
- Vérification du compte utilisé.
- Vérification du contexte de l'événement.
- Analyse des correspondances MITRE ATT&CK.

## Recommandations

- Surveiller les tentatives répétées d'authentification SSH.
- Vérifier les journaux d'authentification.
- Utiliser des clés SSH lorsque cela est adapté.
- Désactiver l'authentification par mot de passe si
  la politique de sécurité le permet.
- Surveiller les connexions SSH provenant de sources inconnues.

## Décision SOC

Événement analysé de plus celle-ci est une simulation contrôlée.

# 📊 Conclusion

L'infrastructure Wazuh a correctement détecté l'événement d'authentification SSH échouée et généré une alerte associée à la règle `5710`.

L'analyse a permis d'identifier :

- La machine concernée
- L'utilisateur utilisé
- L'adresse IP source
- Le service concerné
- Le type d'échec d'authentification
- Le contexte de l'événement

L'événement correspond à une simulation contrôlée réalisée dans le cadre du laboratoire.

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
