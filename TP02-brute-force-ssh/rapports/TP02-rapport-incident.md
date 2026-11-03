# Rapport d'incident SOC – TP02

## 1. Informations générales

- **Référence :** SOC-TP02-2026-001
- **Date :** 03 novembre 2026
- **Période observée :** 16:52:09 à 16:52:27
- **Système concerné :** Ubuntu-VM
- **Poste source :** Windows 11
- **Outil de supervision :** Wazuh 4.14.9
- **Type d'événement :** Tentatives répétées d'authentification SSH échouées
- **Règle Wazuh :** 5710
- **Niveau de sévérité :** 5/15
- **Nombre d'alertes observées :** Au moins 18

## 2. Description de l'événement

Une série de tentatives d'authentification SSH échouées a été simulée depuis un poste Windows 11 vers un serveur Ubuntu supervisé par Wazuh.

Les connexions ont été effectuées avec un nom d'utilisateur inexistant : `utilisateur_inexistant`.

Wazuh a détecté ces événements grâce à la règle 5710 : `Attempt to login using a non-existent user`.

La répétition des tentatives sur une courte période constitue un comportement compatible avec un scénario de force brute.

## 3. Chronologie

| Étape | Événement |
|---|---|
| 16:52:09 | Première tentative SSH échouée observée |
| 16:52:09 – 16:52:27 | Multiples tentatives d'authentification |
| Après les événements | Collecte des journaux par l'agent Ubuntu |
| Détection | Déclenchement répété de la règle Wazuh 5710 |
| Investigation | Analyse de l'adresse IP source et du compte ciblé |
| Conclusion | Qualification d'une simulation contrôlée de tentatives répétées |

## 4. Analyse technique

- **Adresse IP source :** 192.168.1.103
- **Adresse IP cible :** 192.168.1.79
- **Utilisateur ciblé :** `utilisateur_inexistant`
- **Service concerné :** SSH
- **Agent Wazuh :** Ubuntu-VM (002)
- **Poste à l'origine des tentatives :** Windows 11
- **Règle déclenchée :** 5710
- **Niveau d'alerte :** 5/15
- **Résultat :** Authentifications échouées.

Les événements ont été générés depuis le poste Windows 11 à destination du serveur Ubuntu.

La présence de plusieurs alertes rapprochées démontre la capacité de Wazuh à collecter et détecter les tentatives répétées d'authentification SSH.

## 5. Qualification SOC

L'activité observée correspond à une simulation volontaire de tentatives répétées d'authentification SSH dans un environnement de laboratoire contrôlé.

Le comportement présente des caractéristiques similaires à une tentative de force brute.

Cependant, la règle 5710 identifie spécifiquement l'utilisation d'un compte inexistant. Elle ne constitue pas, à elle seule, une confirmation de détection par corrélation d'une attaque par force brute.

Aucun accès non autorisé n'est démontré par les éléments examinés.

## 6. Recommandations

- Surveiller les tentatives d'authentification SSH répétées.
- Identifier les adresses IP à l'origine des connexions suspectes.
- Examiner les comptes ciblés et les journaux d'authentification.
- Vérifier le fonctionnement des règles de corrélation Wazuh.
- Mettre en place une limitation des tentatives de connexion.
- Privilégier l'authentification par clé SSH lorsque cela est adapté à la politique de sécurité.

## 7. Conclusion

Ce TP m'a permis de mettre en pratique la simulation et l'analyse de tentatives répétées d'authentification SSH depuis un poste Windows vers un serveur Ubuntu.

J'ai pu vérifier la collecte des événements par l'agent Ubuntu, leur transmission au SIEM Wazuh et le déclenchement des alertes de sécurité associées.

L'analyse des événements m'a également permis de comprendre la différence entre la détection d'une authentification échouée et la qualification d'un comportement de force brute nécessitant une corrélation.

**Statut final :** Simulation contrôlée – tentatives SSH répétées détectées et analysées.
