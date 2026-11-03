# Rapport d'incident SOC – TP01

## 1. Informations générales

- **Référence :** SOC-TP01-2026-001
- **Date :** 02 novembre 2026
- **Heure :** 15:10:19
- **Système concerné :** Ubuntu-VM
- **Outil de supervision :** Wazuh 4.14.9
- **Type d'événement :** Échec d'authentification SSH
- **Règle Wazuh :** 5710
- **Niveau de sévérité :** 5/15

## 2. Description de l'événement

Une tentative de connexion SSH a été effectuée avec un utilisateur inexistant nommé `user_inexistant`.

Wazuh a détecté cet événement grâce à la règle 5710 : `Attempt to login using a non-existent user`.

## 3. Chronologie

| Étape | Événement |
|---|---|
| 15:10:19 | Tentative de connexion SSH échouée |
| Après l'événement | Collecte par l'agent Ubuntu |
| Détection | Déclenchement de la règle 5710 |
| Investigation | Analyse de l'adresse IP et du compte |
| Conclusion | Qualification de la simulation |

## 4. Analyse technique

- **Adresse IP source :** 127.0.0.1
- **Utilisateur ciblé :** user_inexistant
- **Service concerné :** SSH
- **Agent Wazuh :** Ubuntu-VM (002)
- **Résultat :** Authentification échouée.

L'adresse 127.0.0.1 correspond à localhost. L'événement a donc été généré localement dans l'environnement de laboratoire.

## 5. Qualification SOC

L'événement correspond à une simulation volontaire d'une tentative d'authentification SSH avec un compte inexistant.

Aucun accès non autorisé n'est démontré par les éléments examinés.

## 6. Recommandations

- Surveiller les tentatives d'authentification SSH répétées.
- Examiner les journaux d'authentification.
- Surveiller les connexions provenant d'adresses IP inconnues.
- Privilégier l'authentification par clé SSH lorsque cela est adapté à la politique de sécurité.

## 7. Conclusion

Ce TP m'a permis de mettre en pratique la collecte et l'analyse des événements Linux avec Wazuh, d'identifier une alerte de sécurité, d'examiner son origine et de qualifier l'événement.

**Statut final :** Simulation contrôlée – événement détecté et analysé.
