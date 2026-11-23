# 🖥️ Architecture du laboratoire

```text
┌─────────────────────────────────────┐
│             Windows 11              │
│                                     │
│          Wazuh Agent — 001          │
│          IP : 192.168.1.103         │
│                                     │
│          Journaux Windows           │
│          Sysmon / Security           │
│                                     │
│          Splunk Enterprise          │
│          Version : 10.6.1.1         │
│          Port Web : 8000            │
└──────────────┬───────────┬──────────┘
               │           │
               │           │ Journaux Windows
               │           │ Security / Sysmon
               │           ▼
               │    ┌─────────────────────────┐
               │    │      Splunk SIEM        │
               │    │                         │
               │    │   Recherches SPL        │
               │    │   Règles personnalisées │
               │    │   Alertes DET-WIN-001   │
               │    └─────────────────────────┘
               │
               │ Logs / événements Wazuh
               ▼
┌─────────────────────────────────────┐
│              Wazuh SIEM             │
│                                     │
│          Wazuh Manager              │
│          Wazuh Indexer              │
│          Wazuh Dashboard            │
│                                     │
│          IP : 192.168.1.44          │
│          Version : 4.14.9           │
└──────────────▲──────────────────────┘
               │
               │ Logs / événements Linux
               │
┌──────────────┴──────────────────────┐
│             Ubuntu 26.04.1          │
│                                     │
│          Wazuh Agent — 002          │
│          Nom : Ubuntu-VM            │
│          IP : 192.168.1.79          │
└─────────────────────────────────────┘
```
