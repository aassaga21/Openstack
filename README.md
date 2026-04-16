# Installation d'OpenStack avec MicroStack sur Ubuntu

> **Format :** Documentation HackMD  
> **OS cible :** Ubuntu 25.04 (noble/plume)  
> **Méthode :** MicroStack (snap Canonical)  
> **Auteur :** Alexandra ASSAGA  
> **Date :** Avril 2026

---

## Table des matières

[TOC]

---

## 1. Introduction

### Qu'est-ce qu'OpenStack ?

OpenStack est une plateforme cloud open-source qui permet de déployer et gérer une infrastructure cloud privée. Elle est composée de plusieurs services qui fonctionnent ensemble :

| Service | Nom code | Rôle |
|---------|----------|------|
| Identity | **Keystone** | Gestion des utilisateurs, projets et rôles |
| Compute | **Nova** | Gestion du cycle de vie des VMs |
| Image | **Glance** | Bibliothèque d'images disques (templates OS) |
| Networking | **Neutron** | Réseau virtuel (switches, routeurs, IPs) |
| Block Storage | **Cinder** | Volumes persistants (disques virtuels) |
| Dashboard | **Horizon** | Interface web graphique |

### MicroStack vs Sunbeam

```
┌─────────────────────────────────────────────────────────┐
│  MicroStack (notre approche)   │  Sunbeam (production)  │
├────────────────────────────────┼────────────────────────┤
│  Installation rapide        │  Multi-nœuds              │
│  Nœud unique                │  Haute disponibilité      │
│  Idéal pour tests/démos     │  Scalabilité              │
│  Moins de complexité        │  Production-grade         │
└─────────────────────────────────────────────────────────┘
```

> **Pour un lab ou une démo**, MicroStack est le choix idéal : simple, rapide, tout-en-un.

---

## 2. Prérequis

### 2.1 - Configuration minimale recommandée

| Composant | Minimum | Recommandé |
|-----------|---------|------------|
| RAM | 8 Go | 16 Go |
| CPU | 4 vCPUs | 8 vCPUs |
| Disque | 50 Go | 100 Go |
| OS | Ubuntu 20.04+ | Ubuntu 25.04 |

> **Important :** La virtualisation imbriquée (nested virtualization) doit être activée si vous installez sur une VM.

### 2.2 — Création de la machine virtuelle ubuntu 25.04 sur VMware Worstation

[Télécharger Ubuntu Desktop](https://ubuntu.com/download/desktop)

![image](https://hackmd.io/_uploads/H1iDs763Wg.png)

> Vous pouvez prendre la version de votre choix
![image](https://hackmd.io/_uploads/Bk3diQa2Zl.png)
![image](https://hackmd.io/_uploads/H1S5om63-l.png)
![image](https://hackmd.io/_uploads/HJQjiQ6h-l.png)
![image](https://hackmd.io/_uploads/rk42s76nWl.png)
![image](https://hackmd.io/_uploads/HydTsQp3-g.png)
![image](https://hackmd.io/_uploads/SkGyhXp3Zg.png)
![image](https://hackmd.io/_uploads/B1mx37ahbg.png)
![image](https://hackmd.io/_uploads/Bk7b27T2Zx.png)
![image](https://hackmd.io/_uploads/Bkuf27a2bl.png)

### 2.3 — Configuration de la machine virtuelle ubuntu 25.04 sur VMware Worstation

![image](https://hackmd.io/_uploads/Sk98nm6nZl.png)
![image](https://hackmd.io/_uploads/SysPhQTnWx.png)
![image](https://hackmd.io/_uploads/SJTun7anbg.png)
![image](https://hackmd.io/_uploads/r1l92m6nWe.png)
![image](https://hackmd.io/_uploads/S1-j3m6hWx.png)
![image](https://hackmd.io/_uploads/B1O2hQphZe.png)
![image](https://hackmd.io/_uploads/B1VkTXThZg.png)
![image](https://hackmd.io/_uploads/HJGZaQahbl.png)
![image](https://hackmd.io/_uploads/HyPmTXp3bx.png)
![image](https://hackmd.io/_uploads/Bkjr6mp3Wl.png)
![image](https://hackmd.io/_uploads/SyeP6XT3bx.png)
![image](https://hackmd.io/_uploads/SJpOTQT2bg.png)
![image](https://hackmd.io/_uploads/Bk0KaQT2-e.png)
![image](https://hackmd.io/_uploads/Bkyia7p3Zg.png)
![image](https://hackmd.io/_uploads/HJInTQphbl.png)
![image](https://hackmd.io/_uploads/SkmTTmp2bg.png)
![image](https://hackmd.io/_uploads/r1m0amT3bx.png)

---

## 3. Installation

### 3.1 — Mise à jour du système

```bash
sudo apt update 
sudo apt upgrade
```
![image](https://hackmd.io/_uploads/rkybE7n2-l.png)
![image](https://hackmd.io/_uploads/B16bN733Ze.png)
![image](https://hackmd.io/_uploads/B1AGVX3nWx.png)

---

### 3.2 — Installation du snap MicroStack

```bash
sudo snap install microstack --beta
snap list microstack
```
![image](https://hackmd.io/_uploads/B1GI47n2Wg.png)

**Résultat attendu :**
```
microstack (beta) ussuri from Canonical✓ installed
```
![image](https://hackmd.io/_uploads/HJyqVQ2nWx.png)

> En cas d'erreur réseau, relancer la commande.

---

### 3.3 — Initialisation du cloud OpenStack

Cette commande configure automatiquement tous les services OpenStack. Elle prend **plusieurs minutes**.

```bash
sudo microstack init --auto --control
```
![image](https://hackmd.io/_uploads/SkT1BQhhWx.png)
![image](https://hackmd.io/_uploads/HkW7HQ2hZl.png)

**Résultat attendu (extrait) :**
```
microstack_init - INFO - Configuring clustering ...
microstack_init - INFO - Setting up as a control node.
...
(configuration de RabbitMQ, MySQL, Keystone, Nova, Neutron...)
...
microstack_init - INFO - restarting libvirt and virtlogd ...
microstack_init - INFO - Complete. Marked microstack as initialized!
```

> Le message **"Complete. Marked microstack as initialized!"** confirme le succès.

---

## 4. Vérification de l'installation

### 4.1 — Vérifier l'état des services

```bash
microstack.openstack --version
sudo snap services
```
![image](https://hackmd.io/_uploads/ryqwrX33Ze.png)
![image](https://hackmd.io/_uploads/HyZorm3hbg.png)

**Services importants à vérifier (état `active`) :**
```
Service                         Démarrage   Actuel
microstack.cinder-scheduler     activé   actif
microstack.glance-api           activé   actif
microstack.horizon-uwsgi        activé   actif
microstack.keystone-uwsgi       activé   actif
microstack.libvirtd             activé   actif
microstack.mysqld               activé   actif
microstack.neutron-api          activé   actif
microstack.nova-api             activé   actif
microstack.nova-compute         activé   actif
microstack.rabbitmq-server      activé   actif
```

---

### 4.2 — Tester le client CLI OpenStack

```bash
sudo cat /var/snap/microstack/common/etc/microstack.rc
source /var/snap/microstack/common/etc/microstack.rc
```
![image](https://hackmd.io/_uploads/SJfz3V33bg.png)
![image](https://hackmd.io/_uploads/H18pdmhhWx.png)

```bash
curl -k -i -X POST https://192.168.1.32:5000/v3/auth/tokens \
  -H "Content-Type: application/json" \
  -d '{
    "auth": {
      "identity": {
        "methods": ["password"],
        "password": {
          "user": {
            "name": "admin",
            "domain": {"name": "default"},
            "password": "TON_MDP"
          }
        }
      },
      "scope": {
        "project": {
          "name": "admin",
          "domain": {"name": "default"}
        }
      }
    }
  }'
```
![image](https://hackmd.io/_uploads/H1DDWB23-g.png)
![image](https://hackmd.io/_uploads/BJrOZH23Wx.png)

```bash
curl -k -H "X-Auth-Token: TON_NOUVEAU_TOKEN" \
https://192.168.1.32:5000/v3/projects
```

![image](https://hackmd.io/_uploads/HkT_ZB32-l.png)

```bash
sudo microstack.openstack \
  --os-auth-url https://192.168.1.32:5000/v3 \
  --os-username admin \
  --os-password TON_MOT_DE_PASSE \
  --os-project-name admin \
  --os-user-domain-name default \
  --os-project-domain-name default \
  --os-cacert /var/snap/microstack/common/etc/ssl/certs/cacert.pem \
  image list
```

![image](https://hackmd.io/_uploads/SkZTiN3h-l.png)

**Résultat attendu :**
```
+--------------------------------------+--------+--------+
| ID                                   | Name   | Status |
+--------------------------------------+--------+--------+
| 286edd55-908f-4964-bfe7-25badca0cea6 | cirros | active |
+--------------------------------------+--------+--------+
```

> L'image **cirros** confirme que le cloud est fonctionnel.

---

### 4.3 — Lister les endpoints de services

```bash
sudo microstack.openstack endpoint list
```

Cette commande affiche les URLs internes, publiques et admin de chaque service OpenStack.

---

## 5. Accès au Dashboard Horizon (GUI)

### 5.1 — Trouver l'adresse IP du dashboard

```bash
ip a
```
![image](https://hackmd.io/_uploads/B1xjhVh3-g.png)

> Votre URL sera : `http://<adresse_inet>` (ex : `http://10.13.0.173`)

---

### 5.2 — Récupérer le mot de passe admin

```bash
sudo grep OS_PASSWORD /var/snap/microstack/common/etc/microstack.rc
```
![image](https://hackmd.io/_uploads/S1qGTEnhbl.png)

**Résultat attendu :**
```
export OS_PASSWORD=Z8xTl66EMgVRBGelFH0zvX3vOSuClgVE
```

> Notez ce mot de passe pour la connexion.

---

### 5.3 — Se connecter au dashboard

Ouvrez dans votre navigateur :

```
http://<votre_ip_microstack0>
```
![image](https://hackmd.io/_uploads/HyYm6VnhWg.png)
![image](https://hackmd.io/_uploads/By-46N32-g.png)

**Identifiants :**

| Champ | Valeur |
|-------|--------|
| Domain | `default` |
| User Name | `admin` |
| Password | *(résultat du grep ci-dessus)* |

---

## 6. Aperçu du Dashboard

Une fois connecté, vous accédez à l'interface **Horizon** :

```
Project > Compute > Overview
```
![image](https://hackmd.io/_uploads/Sycr6Enn-e.png)

Le dashboard affiche le **Limit Summary** avec les ressources utilisées :

| Ressource | Utilisé | Limite |
|-----------|---------|--------|
| Instances | 0 | 10 |
| VCPUs | 0 | 20 |
| RAM | 0 Bytes | 50 Gio |
| Volumes | 0 | 10 |
| Volume Storage | 0 Bytes | 1000 Gio |

---

## 7. Modification du mot de passe admin

![image](https://hackmd.io/_uploads/rJpWnfn3Ze.png)
![image](https://hackmd.io/_uploads/SkSu2fhnZl.png)
![image](https://hackmd.io/_uploads/r1ysJHh2Wx.png)

---

## 8. Créer une instances

### 8.1 — Télécharger l'image Debian

```bash
wget https://cloud.debian.org/images/cloud/bookworm/latest/debian-12-genericcloud-amd64.qcow2
```
![image](https://hackmd.io/_uploads/HkZrD-a2Zl.png)
![image](https://hackmd.io/_uploads/ryI9CG63bg.png)

---

### 8.2 — Uploader l'image dans Glance

![image](https://hackmd.io/_uploads/S1CS17phWl.png)
![image](https://hackmd.io/_uploads/r1ks-7pn-g.png)
![image](https://hackmd.io/_uploads/Hkjw8IRn-g.png)

### 8.3 - Créer une paire de clés SSH
![image](https://hackmd.io/_uploads/HkWqxqR3-l.png)
![image](https://hackmd.io/_uploads/HJCZW9C2-l.png)
![image](https://hackmd.io/_uploads/rJNjbq0nWx.png)

### 8.4 - Créer un réseau (Network)
![image](https://hackmd.io/_uploads/r1jqMcR2Zl.png)
![image](https://hackmd.io/_uploads/S1xHI7cA2Wx.png)
![image](https://hackmd.io/_uploads/SJcum9C3Zg.png)
![image](https://hackmd.io/_uploads/HJD8r50hZl.png)
![image](https://hackmd.io/_uploads/ByQ_Sq0nbx.png)

### 8.5 - Créer un Router
![image](https://hackmd.io/_uploads/Hk4CS5R2bx.png)
![image](https://hackmd.io/_uploads/BJK389A2Zg.png)
![image](https://hackmd.io/_uploads/HJcFPc02bx.png)
![image](https://hackmd.io/_uploads/SyI0DqRhZe.png)
![image](https://hackmd.io/_uploads/BkJbd9AnZe.png)

### 8.6 - Créer un Security Group
![image](https://hackmd.io/_uploads/SJsO_5C3Wl.png)
![image](https://hackmd.io/_uploads/ByhdF5AnZg.png)
![image](https://hackmd.io/_uploads/H1u6tqC2Wx.png)
![image](https://hackmd.io/_uploads/BJVo5502Wx.png)
![image](https://hackmd.io/_uploads/SyRJscAn-x.png)
![image](https://hackmd.io/_uploads/r1Vfj9A3bx.png)

### 8.7 - Vérifier un Flavor
![image](https://hackmd.io/_uploads/rkA9scCh-l.png)

### 8.6 - Créer l'instance Debian
![image](https://hackmd.io/_uploads/SkfRUIA2We.png)
![image](https://hackmd.io/_uploads/B1x3l203Wx.png)
![image](https://hackmd.io/_uploads/BJ1Hb2Cnbl.png)
![image](https://hackmd.io/_uploads/Sko8-3RhWe.png)
![image](https://hackmd.io/_uploads/HJX5ZhAnWg.png)
![image](https://hackmd.io/_uploads/Hy4pbnRhZe.png)
![image](https://hackmd.io/_uploads/B1EEM3Ch-e.png)
![image](https://hackmd.io/_uploads/BJNUfn03Zl.png)
![image](https://hackmd.io/_uploads/SkIDzh0nZg.png)
![image](https://hackmd.io/_uploads/ryxKM302Wx.png)
![image](https://hackmd.io/_uploads/Byeqz3A3We.png)
![image](https://hackmd.io/_uploads/HkGiG30h-x.png)
![image](https://hackmd.io/_uploads/Skkhf3R2bl.png)
![image](https://hackmd.io/_uploads/HkPBX2AnWl.png)

### 8.7 - Assigner une Floating IP pour y accéder
![image](https://hackmd.io/_uploads/rJ7mQnC2-x.png)
![image](https://hackmd.io/_uploads/HyGS4h0hZe.png)
![image](https://hackmd.io/_uploads/HJ9PN2A2Zx.png)
![image](https://hackmd.io/_uploads/S1M2E2R2Ze.png)
![image](https://hackmd.io/_uploads/HkmSH3C3be.png)

### 8.9 - Se connecter à la console de la VM
![image](https://hackmd.io/_uploads/BJylFnC3We.png)
![image](https://hackmd.io/_uploads/BkNwK3R2Wg.png)

### 8.8 - Se connecter en SSH à la VM

```bash
find / -name "my-key.pem" 2>/dev/null
chmod 600 /home/alexandra/Téléchargements/my-key.pem
ssh -i /home/alexandra/Téléchargements/my-key.pem debian@10.20.20.104
ssh -i /home/alexandra/Downloads/my-key.pem debian@<FLOATING_IP>
```
![image](https://hackmd.io/_uploads/Hyurv3A2-g.png)

> L'utilisateur par défaut des images cloud Debian est **`debian`**.



---

## 9. Source

- Vidéo de référence : https://www.youtube.com/watch?v=r3E_TGIpQuY

---

> *Documentation rédigée à partir de l'installation MicroStack sur une VM Ubuntu 25.04 — Avril 2026*