# Rapport d'incident — TP03
## Investigation d'une alerte Application Shimming avec Wazuh

**Date de l'investigation :** 5 novembre 2026  
**SIEM :** Wazuh 4.14.9  
**Agent concerné :** Abdullrahman — Windows 11  
**Identifiant de l'agent :** 001

---

## 1. Résumé exécutif

Une alerte de niveau 12 a été remontée par Wazuh à la suite de l'exécution du programme Windows `sdbinst.exe`.

Cette alerte est associée à la technique MITRE ATT&CK T1546.011 — Application Shimming.

L'investigation a porté sur l'identification de l'exécutable, la vérification de sa signature numérique, la comparaison de son empreinte SHA256, l'analyse du registre Windows et la corrélation des événements Sysmon.

Les éléments recueillis sont compatibles avec une activité potentiellement légitime de Windows. Aucun indicateur concret de compromission n'a été confirmé. L'origine initiale du processus reste toutefois inconnue.

## 2. Détails de l'alerte

| Champ | Valeur |
|---|---|
| Date | 05/11/2026 |
| Heure locale | 19:40:00 |
| Règle Wazuh | 92058 |
| Niveau | 12 |
| Processus | sdbinst.exe |
| PID | 21736 |
| Compte | NT AUTHORITY\SYSTEM |
| Système | Windows 11 |
| Événement Sysmon | Event ID 1 |

**Commande observée :**

```text
C:\Windows\System32\sdbinst.exe -m -bg
```

## 3. Chronologie des événements

| Heure locale | Événement |
|---|---|
| 19:40:00.236 | Création de `sdbinst.exe`, PID 21736 |
| 19:40:00.257 | Création de `conhost.exe`, PID 8784, par `sdbinst.exe` |

Le processus `conhost.exe` a été lancé environ 21 millisecondes après `sdbinst.exe`.

Le processus parent initial de `sdbinst.exe`, PID 8728, n'a pas été retrouvé dans les événements de création examinés.

## 4. Analyse technique

### 4.1 Signature numérique

La commande suivante a été exécutée :

```powershell
Get-AuthenticodeSignature "C:\Windows\System32\sdbinst.exe" |
Format-List Status, StatusMessage, SignerCertificate
```

**Résultat :** signature valide, signataire Microsoft Windows.

### 4.2 Empreinte SHA256

```powershell
Get-FileHash "C:\Windows\System32\sdbinst.exe" -Algorithm SHA256
```

**Résultat :**

```text
1E19973DD638EFE37E7A66554C3A8EEDFCF01F8136A29CDCB0C73AE162768180
```

L'empreinte correspond à celle présente dans l'événement Sysmon.

### 4.3 Analyse du registre

La clé `InstalledSDB` existe dans :

```text
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\AppCompatFlags
```

Trois bases de compatibilité ont été observées, dont deux associées à IIS Express.

Ces informations sont cohérentes avec la présence de composants de compatibilité applicative. Elles ne suffisent pas à identifier la base précise concernée par l'exécution étudiée.

### 4.4 Analyse des processus

La relation suivante a été confirmée par Sysmon :

```text
sdbinst.exe (PID 21736)
    └── conhost.exe (PID 8784)
```

L'identité du processus ayant lancé `sdbinst.exe` n'a pas pu être déterminée.

## 5. Cartographie MITRE ATT&CK

| Élément | Référence |
|---|---|
| Technique | T1546.011 |
| Nom | Application Shimming |
| Tactiques | Persistence, Privilege Escalation |

Cette correspondance provient de la règle de détection Wazuh. Elle constitue une piste d'investigation et non une preuve d'exploitation de cette technique.

## 6. Évaluation du risque

**Éléments rassurants :**
- Signature numérique Microsoft valide.
- Hash local identique à celui remonté par Sysmon.
- Emplacement système attendu.
- Présence de bases de compatibilité Windows dans le registre.
- Relation parent-enfant observée et cohérente avec la création d'un processus console.

**Éléments restant à confirmer :**
- Identité du processus parent initial, PID 8728.
- Origine exacte de l'exécution.
- Contexte opérationnel ayant déclenché cette commande.

## 7. Conclusion

L'investigation n'a pas permis de mettre en évidence un indicateur concret de compromission.

Les éléments techniques observés orientent vers une activité probablement légitime liée au mécanisme de compatibilité applicative de Windows.

Cependant, l'identification du processus parent initial reste incomplète. Il n'est donc pas possible de qualifier définitivement l'événement comme bénin.

**Verdict : activité probablement légitime — qualification provisoire, compromission non démontrée.**

## 8. Actions complémentaires recommandées

- Rechercher le processus parent initial dans les journaux disponibles.
- Vérifier le contexte temporel de l'exécution.
- Corréler avec d'autres événements Windows et Sysmon.
- Surveiller les prochaines exécutions de `sdbinst.exe`.
- Conserver les éléments de preuve pour une éventuelle réévaluation.

---

**Rapport réalisé dans le cadre d'un laboratoire personnel SOC avec Wazuh et Sysmon.**
