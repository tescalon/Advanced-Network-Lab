# 🛡️ Home Lab Réseau Avancé & Sécurité Opérationnelle

[![Statut du Projet](https://img.shields.io/badge/Statut-En%20Cours-orange)](./documentation/objectifs.md)
[![Technologies Principales](https://img.shields.io/badge/Tech-pfSense%2C%20Proxmox%2C%20Ansible-blue)](./documentation/architecture.md)
[![Focus Réseau & Sécurité](https://img.shields.io/badge/Focus-R%C3%A9seau%20%26%20Cybers%C3%A9curit%C3%A9-red)](./documentation/rapport_technique.md)

Ce dépôt documente le déploiement d'un Home Lab réseau complexe, virtualisé sur des hôtes **Proxmox VE**, simulant une infrastructure d'entreprise hautement segmentée. Le projet met en évidence la maîtrise du **routage sécurisé (pfSense)**, la gestion des accès distants (**VPN**), l'**automatisation (Ansible)**, et l'**audit/GRC** via des outils professionnels de **Documentation (NetBox)** et de **Monitoring (LibreNMS/Grafana)**.

---

## 🎯 Objectifs du Projet

Ce laboratoire est conçu pour valider une **maîtrise complète des architectures réseaux modernes, de la sécurité opérationnelle et des pratiques de Gouvernance, Risque et Conformité (GRC)**.

* **Routage & Segmentation :** Configurer **pfSense A** comme firewall/routeur inter-VLAN principal pour appliquer des politiques de sécurité strictes, assurant le **principe du moindre privilège**.
* **Virtualisation & Distribution :** Utiliser des conteneurs **LXC** et des **VMs** distribués sur deux hôtes Proxmox (PC A et PC B) pour optimiser les ressources.
* **Contrôle et Visibilité :** Déployer une stack de monitoring professionnelle (**LibreNMS, Grafana, ntopng**) pour la surveillance proactive du réseau et l'analyse des flux.
* **Audit et Documentation (GRC) :** Mettre en place **NetBox** pour l'**IPAM** (Gestion des Adresses IP) et l'inventaire, et **Oxidized** pour la sauvegarde automatisée des configurations, des étapes clés de l'audit et de la **conformité**.
* **Automatisation :** Utiliser **Ansible** pour le déploiement rapide et reproductible des services (IaC - Infrastructure as Code).

---

## 🗺️ Architecture du Home Lab

La topologie s'appuie sur une segmentation forte pour isoler les services (Management, Monitoring, Audit) des postes clients, avec **pfSense A** comme point de contrôle central.

### 🌐 Segmentation VLAN

| ID VLAN | Plage IP | Rôle et Services Hôtes |
| :---: | :--- | :--- |
| **N/A** | `192.168.1.0/24` | WAN/Accès Internet (Box et interface de pfSense A). |
| **10** | `192.168.10.0/24` | **MGMT :** Services de Management (Ansible, NetBox). |
| **20** | `192.168.20.0/24` | **USER :** Postes clients virtuels (Client test VM). |
| **30** | `192.168.30.0/24` | **DMZ/MONITOR :** Services de Monitoring (Grafana, LibreNMS). |
| **40** | `192.168.40.0/24` | **AUDIT/CONFIG :** Services d'Analyse et de Sauvegarde (ntopng, Oxidized). |

### 🧠 Topologie Globale

![Schéma d'architecture du Home Lab virtualisé avec pfSense A et B, Proxmox Dual Host, Segmentation VLANs, et outils de Monitoring.](image_f16831.png)

*Le schéma illustre la répartition logique et la connectivité par VLAN. Le **pfSense B** est utilisé comme Routeur/Firewall Interne pour la Zone d'Audit, permettant une isolation supplémentaire des services d'analyse et de sauvegarde.*

| Composant | Rôle Principal | IP de Management (Exemple) | VLAN | Hôte Proxmox |
| :--- | :--- | :--- | :---: | :--- |
| **pfSense A (VM)** | Firewall, Routage Inter-VLAN, NAT, VPN | `192.168.10.1` | 10 | PC A |
| **pfSense B (VM)** | Routeur/FW Interne Zone Audit | `192.168.20.10` | 20 | PC B |
| **Ansible (LXC)** | Contrôleur d'Automatisation (IaC) | `192.168.10.X` | 10 | PC A |
| **NetBox (LXC)** | IPAM & Inventaire (GRC) | `192.168.10.Y` | 10 | PC A |
| **LibreNMS (LXC)** | Collecte de Données Réseau | `192.168.30.X` | 30 | PC A |
| **Grafana (LXC)** | Visualisation des Métriques | `192.168.30.Y` | 30 | PC A |
| **ntopng (LXC)** | Analyse du Flux Réseau | `192.168.40.X` | 40 | PC B |
| **Oxidized (LXC)** | Sauvegarde de Configuration | `192.168.40.Y` | 40 | PC B |

---

## 💻 Démarrage Rapide et Code

Le déploiement des services est orchestré via Ansible.

1.  **Prérequis :** Les VMs/LXC de base (`Ansible`, `NetBox`, etc.) doivent être déployées manuellement sur Proxmox.
2.  **Lancement :** Exécuter les playbooks à partir du contrôleur Ansible pour l'installation et la configuration des outils.

```bash
# Se connecter au contrôleur Ansible
ssh user@ansible-controller

# Lancer le playbook de déploiement des services de monitoring
ansible-playbook ansible/deploy_monitoring_stack.yml
