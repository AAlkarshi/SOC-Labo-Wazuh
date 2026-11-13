# TP04 – Détection de modifications de fichiers système avec Wazuh (FIM)

## 1. Présentation du scénario

**Objectif :** configurer et tester le mécanisme File Integrity Monitoring (FIM) de Wazuh afin de détecter une modification de fichier sur un système Linux.

**Environnement :**
- SIEM / XDR : Wazuh 4.14.9
- Système surveillé : Ubuntu Linux (Ubuntu-VM)
- Agent Wazuh : ID 002
- Serveur Wazuh : 192.168.1.44
- Répertoire surveillé : `/etc`
- Mécanisme utilisé : Syscheck (FIM)

## 2. Configuration

La configuration du module Syscheck a été vérifiée dans :

`/var/ossec/etc/ossec.conf`

Les paramètres utilisés comprennent :

- `disabled`: no — surveillance activée.
- `frequency`: 43200 — analyse périodique toutes les 12 heures.
- `scan_on_start`: yes — analyse au démarrage de l'agent.
- Répertoires surveillés : `/etc`, `/usr/bin`, `/usr/sbin`, `/bin`, `/sbin` et `/boot`.
- Surveillance en temps réel de `/etc` avec `realtime="yes"`.

Après modification de la configuration, l'agent Wazuh a été redémarré.

<img width="618" height="84" alt="TP04-ConfigRealtimeYes" src="https://github.com/user-attachments/assets/cda9d535-b97d-461a-ae80-42c821d628dc" />


## 3. Simulation de l'événement

Une modification volontaire a été réalisée sur Ubuntu-VM afin de tester la détection FIM.

Fichier utilisé :

`/etc/test-wazuh-fim.txt`

Création du fichier :

```bash
sudo touch /etc/test-wazuh-fim.txt
```

Ajout de contenu :

```bash
echo "Test de surveillance FIM Wazuh" | sudo tee /etc/test-wazuh-fim.txt
```

Une seconde modification a ensuite été effectuée :

```bash
echo "Deuxieme modification FIM" | sudo tee -a /etc/test-wazuh-fim.txt
```

<img width="422" height="72" alt="TP04-ModificationFichierReussi" src="https://github.com/user-attachments/assets/86af46c8-9270-4e97-b43b-8fad9d1bfaf7" />

Cette manipulation permet de simuler une modification de fichier pouvant nécessiter une investigation de sécurité.

## 4. Résultat de la détection

<img width="789" height="424" alt="TP04-AlertesObtenus" src="https://github.com/user-attachments/assets/637db91d-169f-4257-b965-b35f254c15c4" />

L'événement a été remonté dans le Dashboard Wazuh, dans la section Threat Hunting, sur l'agent Ubuntu-VM.

**Alerte observée :**

| Indicateur | Résultat |
|---|---|
| Agent | Ubuntu-VM |
| ID agent | 002 |
| Règle Wazuh | 550 |
| Description | Integrity checksum changed |
| Niveau | 7/15 |
| Date observée | 10 novembre 2026 |
| Heure observée | 18:26:38.926 |

La règle 550 indique que Wazuh a détecté un changement d'intégrité sur un fichier surveillé.

Les événements PAM et sudo observés au même moment (règles 5501, 5502 et 5402) correspondent aux ouvertures et fermetures de session ainsi qu'à l'utilisation des privilèges administrateur pendant les manipulations.

## 5. Analyse SOC

**Constat :**

<img width="388" height="682" alt="TP04-AlertesDetails1" src="https://github.com/user-attachments/assets/c3b5e296-0743-484e-8acf-cf14b966a752" />

<img width="382" height="338" alt="TP04-AlertesDetails2" src="https://github.com/user-attachments/assets/52e2f93e-3873-4266-9ca2-2c4d8945e079" />

Une modification d'intégrité a été détectée sur le système Ubuntu-VM par le module Syscheck de Wazuh.

**Interprétation :**

Le niveau 7 indique une alerte nécessitant une vérification. Il ne constitue pas, à lui seul, une preuve de compromission.

Dans ce scénario, la modification était volontaire et réalisée dans le cadre d'un test contrôlé.

**Éléments complémentaires à examiner lors d'une investigation :**

- Chemin exact du fichier (`syscheck.path`).
- Type d'événement (`syscheck.event`).
- Empreintes avant et après modification (`syscheck.sha256_before` et `syscheck.sha256_after`), si disponibles.
- Taille du fichier avant et après modification.
- Identité de l'utilisateur ayant effectué la modification.
- Corrélation avec les événements d'authentification et d'élévation de privilèges.

## 6. Correspondance avec MITRE ATT&CK

**Référentiel :** MITRE ATT&CK Enterprise.

Le module FIM (File Integrity Monitoring) de Wazuh permet de surveiller les modifications apportées aux fichiers et répertoires sensibles d'un système.

Dans ce scénario, la règle Wazuh 550 signale un changement d'intégrité détecté sur Ubuntu-VM.

<img width="1090" height="324" alt="TP04-MitreAttackExplication" src="https://github.com/user-attachments/assets/f55ef42c-0582-48b4-aba7-4fb6243d789f" />

La surveillance FIM constitue un mécanisme de détection et de supervision. Elle peut contribuer à identifier des comportements associés à différentes techniques MITRE ATT&CK, notamment les modifications de fichiers ou de configurations système.

## 7. Conclusion

Ce TP a permis de configurer et de tester le mécanisme File Integrity Monitoring de Wazuh sur une machine Ubuntu.

La surveillance en temps réel du répertoire `/etc` a permis de détecter une modification volontaire de fichier et de générer une alerte de niveau 7 (règle 550) dans le Dashboard.

Cet exercice démontre la capacité à configurer une surveillance d'intégrité, à identifier une alerte de sécurité et à effectuer une première analyse dans une démarche SOC L1.

**Statut :** test de détection réalisé avec succès. Investigation détaillée des empreintes et des métadonnées à compléter à partir du détail de l'alerte.
