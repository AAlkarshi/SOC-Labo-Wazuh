# TP04 – File Integrity Monitoring (FIM) avec Wazuh

## Présentation

Ce TP présente la mise en place et le test du mécanisme **File Integrity Monitoring (FIM)** de Wazuh sur une machine Ubuntu Linux.

L'objectif est de détecter les modifications apportées aux fichiers et répertoires sensibles du système, puis d'analyser les alertes générées depuis le Dashboard Wazuh.

## Objectifs

- Configurer le module Syscheck (FIM).
- Surveiller les répertoires système Linux.
- Activer la surveillance en temps réel sur `/etc`.
- Simuler une modification de fichier.
- Détecter et analyser une alerte d'intégrité.
- Comprendre la sévérité et le contexte d'un événement de sécurité.

## Environnement technique

| Composant | Configuration |
|---|---|
| SIEM | Wazuh 4.14.9 |
| Système surveillé | Ubuntu Linux |
| Agent | Ubuntu-VM (ID 002) |
| Serveur Wazuh | 192.168.1.44 |
| Module | Syscheck / FIM |

## Configuration FIM

Fichier de configuration :

`/var/ossec/etc/ossec.conf`

Principaux paramètres :

- Surveillance activée (`disabled="no"`).
- Analyse périodique toutes les 12 heures (`frequency="43200"`).
- Analyse au démarrage (`scan_on_start="yes"`).
- Répertoires surveillés : `/etc`, `/usr/bin`, `/usr/sbin`, `/bin`, `/sbin` et `/boot`.
- Surveillance en temps réel de `/etc` (`realtime="yes"`).

Exemple de configuration :

```xml
<directories realtime="yes">/etc</directories>
```

<img width="618" height="84" alt="TP04-ConfigRealtimeYes" src="https://github.com/user-attachments/assets/d3c4f3b7-898e-42e9-bfca-1f85a2b126d3" />

## Scénario de test

Création d'un fichier de test dans `/etc` :

```bash
sudo touch /etc/test-wazuh-fim.txt
```

Ajout de contenu :

```bash
echo "Test de surveillance FIM Wazuh" | sudo tee /etc/test-wazuh-fim.txt
```

Modification du fichier :

<img 4e1c-95be-1e10b7764e86" />

```bash
echo "Deuxieme modification FIM" | sudo tee -a /etc/test-wazuh-fim.txt
```

width="422" height="72" alt="TP04-ModificationFichierReussi" src="https://github.com/user-attachments/assets/952d08ff-f82e-

## Résultat obtenu

La modification a été détectée par Wazuh et remontée dans Threat Hunting.

| Indicateur | Résultat |
|---|---|
| Agent | Ubuntu-VM |
| Règle | 550 |
| Description | Integrity checksum changed |
| Niveau | 7/15 |
| État | Détection réalisée |

L'alerte confirme le fonctionnement du mécanisme de surveillance d'intégrité.

## Analyse de sécurité

Une modification inattendue d'un fichier système peut être le signe :
- D'une modification de configuration non autorisée.
- D'une activité malveillante.
- D'une tentative de persistance.
- D'une modification légitime effectuée par un administrateur.

Une investigation complémentaire doit permettre d'identifier le fichier concerné, le type de modification, les empreintes cryptographiques et l'utilisateur à l'origine de l'action.

Dans ce TP, la modification était volontaire et réalisée dans un environnement de laboratoire.

## Compétences travaillées

- Administration Linux.
- Configuration d'un agent Wazuh.
- File Integrity Monitoring (FIM).
- Surveillance en temps réel.
- Analyse d'alertes SIEM.
- Investigation de premier niveau (SOC L1).
- Interprétation des événements de sécurité.

## Structure du TP

```text
TP04-DétectionModificationsFichiers(FIM)/
│
├── README.md
│
├── rapports/
│   └── TP04-rapport-incident.md
│
└── screenshots/
    ├── TP04-AlertesDetails1.png
    ├── TP04-AlertesDetails2.png
    └── TP04-AlertesObtenus.png
    └── TP04-MitreAttackExplication.png
    └── TP04-ConfigRealtimeYes.png
    └── TP04-ModificationFichierReussi.png
```

## Conclusion

Ce laboratoire démontre la capacité de Wazuh à détecter les changements d'intégrité sur un système Linux grâce au module Syscheck.

La détection d'une modification volontaire dans `/etc` et la remontée de l'alerte 550 de niveau 7 illustrent une situation concrète d'investigation pour un analyste SOC L1.

**Statut : Détection FIM réalisée avec succès.**
