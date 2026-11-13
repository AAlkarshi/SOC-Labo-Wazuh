# TP03 — Investigation d'une alerte Sysmon : Application Shimming

## 1. Présentation du TP

Ce 3ème exercice consiste à investiguer une alerte de sécurité Windows remontée par Sysmon et centralisée dans Wazuh.

L'objectif est d'analyser l'exécution du processus `sdbinst.exe`, associé au mécanisme Windows Application Compatibility, et de déterminer si les éléments observés présentent des signes de compromission.

**Compétences travaillées :**
- Supervision de postes Windows avec Wazuh.
- Collecte et analyse des événements Sysmon.
- Investigation d'une alerte de sécurité.
- Analyse des processus et de leurs relations parent-enfant.
- Vérification d'une signature numérique et d'une empreinte SHA256.
- Analyse du registre Windows.
- Référencement des techniques MITRE ATT&CK.
- Qualification et documentation d'un événement de sécurité.

## 2. Environnement technique

| Composant | Configuration |
|---|---|
| SIEM | Wazuh 4.14.9 |
| Système supervisé | Windows 11 Home |
| Agent Wazuh | Abdullrahman — ID 001 |
| Collecte des événements | Sysmon 15.22 |
| Type d'événement | Sysmon Event ID 1 — Process Create |

## 3. Détection

Wazuh a généré une alerte concernant l'exécution de `sdbinst.exe`

**Détection de l'alerte :**

<img width="1095" height="521" alt="TP3-Alerte-wazuh-regle-92058" src="https://github.com/user-attachments/assets/55512f7d-8282-46a9-8027-7d6dd5b9cdd2" />



**Informations détaillés :**

<img width="547" height="786" alt="TP3-Detail-Evenement-SYSMON1" src="https://github.com/user-attachments/assets/8a286f9f-09a0-4414-8f7d-c49779bd44bd" />

<img width="527" height="580" alt="TP3-Detail-Evenement-SYSMON2" src="https://github.com/user-attachments/assets/7a86768f-46cd-4e61-82a0-6679eec89a50" />

**Informations de l'alerte :**

| Champ | Valeur |
|---|---|
| Règle Wazuh | 92058 |
| Niveau | 12/15 |
| Description | Application Compatibility Database launched |
| Processus | sdbinst.exe |
| PID | 21736 |
| Compte | NT AUTHORITY\SYSTEM |
| Commande | `C:\Windows\System32\sdbinst.exe -m -bg` |
| Technique MITRE associée à la règle | T1546.011 — Application Shimming |



## 4. Investigation

### 4.1 Vérification de l'exécutable

<img width="1091" height="617" alt="TP3-VerificationSHAetsignature" src="https://github.com/user-attachments/assets/61d01d47-ba26-465a-ab8f-52f2f9a57099" />

L'exécutable a été vérifié à l'emplacement : `C:\Windows\System32\sdbinst.exe`

La signature numérique a été contrôlée avec PowerShell :

```powershell
Get-AuthenticodeSignature "C:\Windows\System32\sdbinst.exe" |
Format-List Status, StatusMessage, SignerCertificate
```

Résultat observé :
- Statut : `Valid`
- Signataire : Microsoft Windows
- Éditeur : Microsoft Corporation

### 4.2 Vérification de l'empreinte SHA256

```powershell
Get-FileHash "C:\Windows\System32\sdbinst.exe" -Algorithm SHA256
```

Empreinte obtenue :

`1E19973DD638EFE37E7A66554C3A8EEDFCF01F8136A29CDCB0C73AE162768180`

Cette empreinte correspond à celle remontée dans l'événement Sysmon transmis à Wazuh.

### 4.3 Analyse du registre Windows

<img width="1097" height="727" alt="TP3-RegistreAnalyse-Installed-sdb" src="https://github.com/user-attachments/assets/8ba9b855-6974-487f-9088-eca8b3910d7c" />

La clé suivante a été examinée :

`HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\InstalledSDB`

Plusieurs bases de compatibilité ont été recensées, notamment :
- IIS Express Application Compatibility Database for x64.
- IIS Express Application Compatibility Database for x86.
- Microsoft Windows Application Compatibility Fix Database.

Ces éléments apportent du contexte sur l'utilisation du mécanisme de compatibilité applicative de Windows. Ils ne prouvent pas, à eux seuls, que l'exécution observée était légitime.

### 4.4 Corrélation des processus Sysmon

<img width="960" height="777" alt="TP3-Sysmon-evenements" src="https://github.com/user-attachments/assets/b20b3eaf-1253-4577-b139-838fb1dab7f4" />

L'analyse des événements Sysmon a permis d'établir la relation suivante :

```text
sdbinst.exe
PID : 21736
Commande : sdbinst.exe -m -bg
       |
       └── conhost.exe
           PID : 8784
```

L'événement de création de `conhost.exe` indique explicitement :

`ParentImage: C:\Windows\System32\sdbinst.exe`

Le processus parent initial de `sdbinst.exe`, PID 8728, n'a pas pu être identifié avec les journaux examinés.

## 5. Analyse MITRE ATT&CK

La règle Wazuh associe cet événement à :

- Technique : T1546.011
- Nom : Application Shimming
- Tactiques associées : Persistence et Privilege Escalation.

L'Application Shimming est une fonctionnalité Windows pouvant être détournée à des fins de persistance ou d'exécution de code. Cependant, la présence d'une alerte associée à cette technique ne signifie pas automatiquement qu'une attaque a eu lieu. Cette technique permet de détourner des mécanismes de compatibilité applicative pour maintenir une présence ou obtenir des privilèges.

## 6. Conclusion de l'investigation

Les vérifications effectuées ont permis de constater :

- Une signature numérique Microsoft valide.
- Une empreinte SHA256 concordante entre le fichier local et l'événement Sysmon.
- Un exécutable situé dans le répertoire système Windows.
- La présence de bases de compatibilité applicative dans le registre.
- Une relation parent-enfant identifiée entre `sdbinst.exe` et `conhost.exe`.

Aucun indicateur concret de compromission n'a été confirmé au cours de cette investigation.

Toutefois, le processus parent initial (PID 8728) n'a pas pu être identifié. L'origine exacte de l'exécution reste donc à confirmer.

**Qualification : activité probablement légitime, sans compromission démontrée — conclusion provisoire.**

## 7. Compétences démontrées

Ce TP illustre une démarche d'investigation SOC :

1. Détection d'un événement par le SIEM.
2. Collecte des données Sysmon.
3. Analyse du processus déclencheur.
4. Vérification de l'intégrité et de l'éditeur du fichier.
5. Corrélation des événements.
6. Analyse du contexte Windows.
7. Qualification prudente de l'alerte.
8. Documentation des résultats.

## 8. Captures d'écran

Les preuves techniques sont disponibles dans le dossier [`screenshots/`](screenshots/).

| Capture | Description |
|---|---|
| 01-alerte-wazuh-92058.png | Alerte Wazuh |
| 02-details-evenement-sysmon.png | Détails de l'événement |
| 03-signature-et-hash.png | Vérification de la signature et du hash |
| 04-registre-installed-sdb.png | Analyse du registre |
| 05-correlation-processus.png | Corrélation des processus |

---

**Projet personnel — Home SOC Lab**

Environnement de laboratoire à des fins pédagogiques. Les conclusions sont limitées aux éléments collectés et analysés.
