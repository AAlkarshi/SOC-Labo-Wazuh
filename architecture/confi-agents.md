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

L'agent est ensuite apparu dans le tableau de bord Wazuh avec le statut : **Active** ainsi que les différents agents actifs ci-dessous.

<img width="942" height="442" alt="image Wazuh Agents" src="https://github.com/user-attachments/assets/e2101cb6-ccbf-4c59-b812-a2fd8ee009db" />
