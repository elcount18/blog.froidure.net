---
title: "Désactiver les notifications Enterprise Edition"
date: 2025-01-08 09:00:00 +0100
published: true
categories:
  - Infrastructure
tags:
  - Proxmox
  - Virtualisation
  - Homelab
excerpt: "Proxmox : Désactiver les notifications Enterprise Edition"
header:
  overlay_image: /assets/images/headers/2025-01-01-proxmox-banner.png
  overlay_filter: 0.5
toc: true
toc_sticky: true
---

Si vous utilisez Proxmox VE dans un environnement personnel (ou de test comme pour le blog), les notifications constantes de l'édition Enterprise peuvent devenir agaçantes.
Voici comment les désactiver proprement en basculant vers les dépots de la Community Edition

## Contexte

Par défaut, Proxmox VE est configuré pour utiliser les dapots Enterprise Edition. Sans souscription valide, vous recevez régulièrement :
- Des notifications dans l'interface web
- Des avertissements lors des mises à jour
- Des limitations lors de l'accès à certains dépots

[Souscription invalide](/assets/images/posts/2025-01-proxmox/pve-invalid-sub.png)

## Solution (new)

Ok, j'avais écris tout ce qu'il y a en dessous avant de découvrir ce [petit script magique](https://community-scripts.github.io/ProxmoxVE/scripts?id=post-pve-install) qui permet de faire pleins de choses en une fois en post-install de Proxmox :

```bash
    ____ _    ________   ____             __     ____           __        ____
   / __ \ |  / / ____/  / __ \____  _____/ /_   /  _/___  _____/ /_____ _/ / /
  / /_/ / | / / __/    / /_/ / __ \/ ___/ __/   / // __ \/ ___/ __/ __ `/ / /
 / ____/| |/ / /___   / ____/ /_/ (__  ) /_   _/ // / / (__  ) /_/ /_/ / / /
/_/     |___/_____/  /_/    \____/____/\__/  /___/_/ /_/____/\__/\__,_/_/_/

 ✓ Corrected Proxmox VE Sources
 ✓ Disabled 'pve-enterprise' repository
 ✓ Enabled 'pve-no-subscription' repository
 ✓ Corrected 'ceph package repositories'
 ✓ Added 'pvetest' repository
 ✓ Disabled subscription nag (Delete browser cache)
 ✓ Disabled high availability
 ✓ Updated Proxmox VE
 ✗ Selected no to Rebooting Proxmox VE (Reboot recommended)
 ✓ Completed Post Install Routines
```

Il est d'ailleurs conseillé de répondre "yes" a toutes les questions.

Cela fait un moment que je n'ai pas utilisé Proxmox, je suis passé sur du baremetal depuis quelques années, mais pour ce blog c'était tout indiqué afin d'avancer dans mon apprentissage, mais aussi le partager avec ceux qui liront un jour ces pages.

## Solution (old)

La solution consiste à désactiver le dépot Enterprise et configurer le dépot Community à la place. C'est faisable soit via l'interface web (via l'outil "shell" en haut a droite), soit en CLI en se connectant en SSH à la machine. Ici vous taperez :

```bash
cd /etc/apt/sources.list.d
mv pve-enterprise.list pve-enterprise.list.disabled
echo 'deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription' > pve-community.list
apt update
apt upgrade -y
```

### Décomposons :

- On se déplace dans le dossier qui contient les sources
- On désactive le fichier qui contient les sources Enterprise
- On ajoute un fichier qui contient les sources Community
- On met a jour la liste des paquets disponibles
- On met a jour les paquets en utilisant la liste COmmunity Edition

## Verification

Après avoir exécuté les commandes :
- Les notifications de souscription disparaîtront
- Les mises à jour utiliseront le dépot Community
- L'interface web n'affichera plus d'avertissements

## Points d'attention

- Les fonctionnalités de base restent identiques
- Seul le support entreprise n'est plus disponible
- Pensez a souscrire a une licence si vous utilisez Proxmox en production

## Pour aller plus loin

- [Documentation sur les dépots Proxmox VE](https://pve.proxmox.com/wiki/Package_Repositories)
- [Guide d'admimnistration officiel de Proxmox](https://pve.proxmox.com/pve-docs/pve-admin-guide.html)
