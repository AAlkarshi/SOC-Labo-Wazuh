# Glossaire du laboratoire SOC – Wazuh

Ce glossaire présente les principaux outils, systèmes et règles de détection utilisés dans le laboratoire SOC. Il permet de comprendre le rôle de chaque composant et le vocabulaire technique rencontré au cours des travaux pratiques.

## A. Glossaire des outils

| Outil | Fonction dans le laboratoire |
|---|---|
| **Wazuh** | Plateforme SIEM/XDR utilisée pour collecter, centraliser, analyser et détecter les événements de sécurité provenant des machines supervisées. |
| **OpenSSH** | Service permettant d'établir des connexions sécurisées à distance via le protocole SSH. Il génère notamment les journaux d'authentification analysés dans les TP. |
| **Sysmon** | Outil Windows qui collecte des événements système détaillés, notamment sur les processus, les connexions réseau et les activités des applications. |
| **Ubuntu** | Système d'exploitation Linux utilisé comme machine cible pour les scénarios de sécurité et les tests d'authentification SSH. |
| **Windows 11** | Système d'exploitation utilisé comme poste source pour effectuer les simulations et générer des événements de sécurité. |
| **VirtualBox** | Logiciel de virtualisation permettant de créer et d'exécuter les machines virtuelles constituant le laboratoire SOC. |
| **MITRE ATT&CK** | Référentiel qui répertorie les tactiques et techniques couramment utilisées par les attaquants. Il permet de contextualiser les comportements observés. |

## B. Glossaire des règles Wazuh

Les règles Wazuh permettent d'identifier des événements de sécurité à partir de conditions définies. Certaines règles détectent un événement isolé, tandis que d'autres peuvent corréler plusieurs événements.

| Règle | Signification | Utilisation dans le laboratoire |
|---|---|---|
| **5710** | Détecte une tentative de connexion SSH avec un utilisateur inexistant. | Observée dans les TP01 et TP02 lors des tentatives d'authentification avec un compte non valide. |
| **5703** | Règle associée aux tentatives d'authentification SSH répétées, selon les conditions de corrélation définies dans la configuration Wazuh. | À tester et à confirmer dans le TP02 afin de vérifier si plusieurs échecs rapprochés déclenchent cette détection. |

> **Remarque :** une alerte individuelle de la règle 5710 ne suffit pas à confirmer une attaque par force brute. Il faut analyser la répétition des événements et vérifier si une règle de corrélation se déclenche.

## C. Vocabulaire SOC et cybersécurité

| Terme | Définition |
|---|---|
| **SIEM** | Solution qui centralise et analyse les journaux afin de détecter les événements de sécurité. |
| **XDR** | Approche de détection et de réponse qui regroupe des données issues de plusieurs environnements informatiques. |
| **Agent Wazuh** | Composant installé sur une machine supervisée pour collecter et transmettre ses événements au serveur Wazuh. |
| **Log (journal)** | Enregistrement d'un événement produit par un système, une application ou un service. |
| **Alerte** | Notification générée lorsqu'un événement correspond aux conditions d'une règle de détection. |
| **Corrélation** | Mise en relation de plusieurs événements afin d'identifier un comportement potentiellement suspect. |
| **Force brute (Brute Force)** | Technique consistant à multiplier les tentatives d'authentification pour essayer de découvrir des identifiants valides. |
| **IOC (Indicator of Compromise)** | Indicateur technique pouvant révéler une compromission, comme une adresse IP suspecte ou un fichier malveillant. |
| **MITRE ATT&CK** | Base de connaissances permettant de classer les tactiques et techniques utilisées lors d'attaques informatiques. |

---

*Ce glossaire sera enrichi progressivement au fil des travaux pratiques et des nouvelles règles de détection étudiées.*
