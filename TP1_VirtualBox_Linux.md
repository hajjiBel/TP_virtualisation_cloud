# Travaux Pratiques : Installation d'une Machine Virtuelle Linux sur VirtualBox

## Objectifs

À la fin de ce TP, vous serez capable de :
- Télécharger et installer VirtualBox
- Créer une nouvelle machine virtuelle
- Configurer les paramètres matériels (RAM, CPU, stockage)
- Comprendre les différentes options de stockage
- Installer un système d'exploitation Linux (Ubuntu Server)
- Accéder et utiliser la machine virtuelle

## Prérequis

- Ordinateur avec au minimum 4 GB de RAM disponible
- Au moins 30 GB d'espace disque libre
- Accès à Internet pour télécharger les images ISO
- Droits administrateur sur votre machine hôte

## Étape 1 : Téléchargement de VirtualBox

### 1.1 Accéder au site officiel

1. Ouvrez votre navigateur web
2. Rendez-vous sur [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
3. Sélectionnez votre système d'exploitation hôte (Windows, macOS ou Linux)

### 1.2 Télécharger l'installateur

- Cliquez sur le fichier d'installation correspondant à votre OS
- Le téléchargement commencera automatiquement
- Attendez la fin du téléchargement (taille environ 120-150 MB)

### 1.3 Installation

**Sur Windows :**
1. Double-cliquez sur le fichier `.exe` téléchargé
2. Acceptez les conditions d'utilisation
3. Choisissez le dossier d'installation (par défaut : `C:\Program Files\Oracle\VirtualBox`)
4. Cochez "Create Start Menu Entries" et "Create Desktop Shortcut"
5. Cliquez sur "Install" et attendez la fin de l'installation
6. Redémarrez votre ordinateur si nécessaire

**Sur macOS :**
1. Ouvrez le fichier `.dmg`
2. Double-cliquez sur l'icône VirtualBox.pkg
3. Suivez l'assistant d'installation
4. Entrez votre mot de passe administrateur

**Sur Linux :**
```bash
sudo apt-get update
sudo apt-get install virtualbox virtualbox-ext-pack
```

## Étape 2 : Téléchargement de l'image ISO Linux

### 2.1 Choisir une distribution

Pour ce TP, nous utiliserons **Ubuntu Server 24.04 LTS** (Long Term Support)

### 2.2 Télécharger l'ISO

1. Rendez-vous sur [https://ubuntu.com/download/server](https://ubuntu.com/download/server)
2. Cliquez sur "Download Ubuntu Server 24.04 LTS"
3. Attendez la fin du téléchargement (environ 1.3 GB)
4. Notez l'emplacement du fichier ISO sur votre disque

## Étape 3 : Création d'une nouvelle machine virtuelle

### 3.1 Lancer VirtualBox

1. Double-cliquez sur l'icône VirtualBox sur votre bureau
2. Attendez le chargement de l'application
3. Vous verrez la fenêtre principale de VirtualBox

### 3.2 Créer une nouvelle machine

1. Cliquez sur le bouton "New" (ou "Nouvelle") dans la barre d'outils
2. Une fenêtre de dialogue s'affiche

### 3.3 Configuration de base

Dans le formulaire qui apparaît :

| Champ | Valeur | Description |
|-------|--------|-------------|
| Name | `Ubuntu-Server-TP` | Nom de la machine virtuelle |
| Machine Folder | Dossier par défaut | Emplacement des fichiers VM |
| ISO Image | Sélectionner l'ISO Ubuntu | Pointez vers le fichier ISO téléchargé |
| Type | Linux | Type du système d'exploitation |
| Version | Ubuntu (64-bit) | Version Linux spécifique |
| Skip Unattended Installation | Cochez cette case | Pour contrôler l'installation manuellement |

Cliquez sur "Next" pour continuer.

### 3.4 Allocation des ressources

Configurez les ressources matérielles :

| Ressource | Valeur | Justification |
|-----------|--------|---------------|
| Base Memory (RAM) | 2048 MB (2 GB) | Minimum pour Ubuntu Server |
| Processors | 2 | Utiliser 2 CPU du système hôte |
| Video Memory | 16 MB | Suffisant pour une interface textuelle |

**Note :** Si votre machine hôte dispose de plus de ressources, vous pouvez augmenter ces valeurs.

Cliquez sur "Next".

## Étape 3.5 : Comprendre les options de stockage

Avant de créer le disque dur virtuel, il est crucial de comprendre les différentes options disponibles.

### 3.5.1 Types de fichiers de disque virtuel

VirtualBox propose plusieurs formats pour stocker le disque virtuel :

#### **VDI (VirtualBox Disk Image)**
- **Format natif de VirtualBox**
- **Portabilité** : Peut être converti vers d'autres formats
- **Performance** : Bonne performance générale
- **Taille de fichier** : Formats dynamiques et fixes disponibles
- **Utilisation recommandée** : Usage général, développement local
- **Avantages** :
  - Format natif optimisé pour VirtualBox
  - Bonne compression possible
  - Support des snapshots amélioré
- **Inconvénients** :
  - Moins portable que VMDK
  - Non compatible directement avec d'autres hyperviseurs

#### **VMDK (Virtual Machine Disk Format)**
- **Format standardisé** : Utilisé par VMware et autres hyperviseurs
- **Portabilité** : Très portable entre hyperviseurs
- **Performance** : Performance similaire à VDI
- **Utilisation recommandée** : Migration vers d'autres plateformes
- **Avantages** :
  - Très compatible avec VMware
  - Portable sur plusieurs hyperviseurs
  - Largement supporté dans l'industrie
- **Inconvénients** :
  - Support des snapshots moins optimisé dans VirtualBox
  - Généralement plus volumineux que VDI

#### **HDD (Parallels Hard Disk)**
- **Format Parallels** : Format propriétaire de Parallels Desktop
- **Performance** : Bonne performance
- **Utilisation recommandée** : Migration depuis Parallels Desktop
- **Avantages** :
  - Compatible avec Parallels Desktop
  - Bonne performance
- **Inconvénients** :
  - Moins portable
  - Compatibilité limitée

#### **QED (QEMU Enhanced Disk)**
- **Format QEMU** : Format d'hyperviseur QEMU/KVM
- **Performance** : Bonne performance avec QEMU
- **Utilisation recommandée** : Environnements KVM/QEMU
- **Avantages** :
  - Optimisé pour QEMU
  - Meilleure performance avec KVM
- **Inconvénients** :
  - Support limité dans VirtualBox
  - Moins documenté

#### **VHDX (Hyper-V Virtual Hard Disk Format)**
- **Format Microsoft** : Format natif de Hyper-V
- **Performance** : Très bonne performance
- **Utilisation recommandée** : Migration vers Hyper-V
- **Avantages** :
  - Natif pour Hyper-V
  - Très compatible avec l'écosystème Microsoft
- **Inconvénients** :
  - Support limité dans VirtualBox
  - Format plus récent, moins largement supporté

### 3.5.2 Mode d'allocation du disque

#### **Allocation dynamique (Dynamically Allocated)**

```
Mode : Allocation à la demande
Taille initiale : Petite (quelques MB)
Croissance : Augmente automatiquement selon les besoins
```

**Caractéristiques :**
- Le disque démarre petit et grandit au fur et à mesure
- Utilise seulement l'espace réellement utilisé
- Taille initiale du fichier : très petite (env. 1-2 MB)
- Croissance progressive jusqu'à la taille maximale définie

**Avantages :**
- Économise de l'espace disque sur le disque hôte
- Flexible pour les environnements de développement
- Idéal quand vous n'êtes pas certain de l'espace nécessaire
- Permet d'allouer des disques plus grands sans gaspiller d'espace

**Inconvénients :**
- Performance légèrement inférieure (fragmentation possible)
- Croissance du disque peut causer des ralentissements
- Ne peut pas être défragmenté de manière standard
- Risque de remplir le disque hôte si mal dimensionné

**Cas d'usage idéal :**
- Machines virtuelles de développement
- Environnements d'apprentissage et de test
- Quand l'espace disque hôte est limité
- Pour les VM temporaires

#### **Allocation fixe (Fixed Size)**

```
Mode : Allocation immédiate
Taille : Entièrement allouée dès la création
Croissance : Aucune (taille fixe)
```

**Caractéristiques :**
- Tout l'espace est pré-alloué lors de la création
- Taille du fichier = taille maximale définie
- Pas de croissance possible
- Accès plus rapide aux données

**Avantages :**
- Meilleures performances (pas de fragmentation)
- Vitesse d'accès disque plus rapide
- Pas de ralentissement lors de la croissance
- Prévisibilité totale de l'espace utilisé
- Performance cohérente
- Meilleur pour les charges de production

**Inconvénients :**
- Consomme immédiatement tout l'espace sur le disque hôte
- Gaspille de l'espace si la VM n'utilise pas toute la capacité
- Moins flexible pour les environnements changeants
- Nécessite d'estimer précisément les besoins dès le départ

**Cas d'usage idéal :**
- Machines virtuelles de production
- Serveurs nécessitant des performances optimales
- Quand vous avez amplement d'espace disque hôte
- Bases de données ou applications critiques

### 3.5.3 Contrôleur de disque dur

VirtualBox supporte différents contrôleurs pour le disque dur :

#### **IDE (Integrated Drive Electronics)**
- **Type d'interface** : Interface disque classique
- **Performance** : Performance modérée
- **Compatibilité** : Excellente avec les anciens OS
- **Utilisation** : Systèmes hérités, dépannage

#### **SATA (Serial ATA)**
- **Type d'interface** : Interface disque moderne
- **Performance** : Très bonne performance
- **Compatibilité** : Excellente avec les systèmes modernes
- **Utilisation recommandée** : **Meilleur choix pour la plupart des cas**
- **Caractéristiques** :
  - Support du NCQ (Native Command Queuing)
  - Performance supérieure à l'IDE
  - Compatible avec tous les OS modernes

#### **SAS (Serial Attached SCSI)**
- **Type d'interface** : Interface serveur haute performance
- **Performance** : Excellent
- **Compatibilité** : Bonne avec les OS serveur
- **Utilisation** : Environnements serveur, entreprise

#### **SCSI (Small Computer System Interface)**
- **Type d'interface** : Interface disque professionnelle
- **Performance** : Excellente
- **Compatibilité** : Variable selon l'OS
- **Utilisation** : Compatibilité avec d'anciens systèmes

### 3.5.4 Tableau de synthèse : Quand utiliser quoi ?

| Scénario | Format | Allocation | Contrôleur |
|----------|--------|-----------|-----------|
| Développement/Test | VDI | Dynamique | SATA |
| Apprentissage | VDI | Dynamique | SATA |
| Production | VDI ou VMDK | Fixe | SATA |
| Migration VMware | VMDK | Fixe | SATA |
| Migration Hyper-V | VHDX | Fixe | SATA |
| Performance critique | VDI | Fixe | SAS |
| Espace disque limité | VDI | Dynamique | SATA |

## Étape 3.6 : Configuration du stockage (suite pratique)

Pour ce TP, nous recommandons :

1. Sélectionnez "Create a Virtual Hard Disk Now"
2. **Type de disque : VDI** (format natif VirtualBox)
3. **Allocation du disque : Dynamically allocated** (recommandé pour apprentissage)
4. **Contrôleur : SATA** (sélectionné par défaut)
5. **Taille du disque : 20 GB** minimum pour Ubuntu Server
6. Cliquez sur "Create"

Attendez la création du disque virtuel (rapide avec allocation dynamique).

### Justification pour ce TP :

- **VDI** : Format natif, meilleur support des snapshots
- **Dynamique** : Économise l'espace disque (important en environnement d'apprentissage)
- **SATA** : Performance suffisante pour une VM de test
- **20 GB** : Espace suffisant pour Ubuntu Server + quelques applications

## Étape 3.7 : Vérification des paramètres

Vérifiez tous les paramètres affichés dans le résumé :
- Nom de la machine
- RAM assignée
- Nombre de processeurs
- Type de disque (VDI)
- Taille du disque (20 GB)
- Contrôleur (SATA)

Cliquez sur "Finish" pour terminer la création.

## Étape 4 : Installation du système d'exploitation Linux

### 4.1 Démarrer la machine virtuelle

1. Sélectionnez votre machine virtuelle dans la liste de gauche
2. Cliquez sur le bouton "Start" (vert) dans la barre d'outils
3. La machine virtuelle démarre et affiche l'écran de démarrage Ubuntu

### 4.2 Suivre l'assistant d'installation

L'écran affichera plusieurs étapes :

**Sélection de la langue :**
- Naviguez avec les flèches du clavier
- Sélectionnez votre langue (par exemple, "English" ou "Français")
- Appuyez sur "Entrée"

**Mise à jour de l'installeur :**
- Choisissez "Continue without updating"

**Clavier :**
- Sélectionnez votre disposition clavier
- Pour azerty français : choisissez "French"

**Réseau :**
- L'interface réseau devrait être détectée automatiquement
- Si DHCP est activé, une adresse IP sera assignée automatiquement
- Cliquez sur "Done"

**Miroir Ubuntu :**
- Acceptez le miroir par défaut proposé
- Cliquez sur "Done"

**Partitionnement du disque :**
1. Sélectionnez "Use An Entire Disk"
2. Choisissez le disque créé (généralement `/dev/sda`)
3. Confirmez le schéma de partitionnement proposé
4. Cliquez sur "Done"

**Informations utilisateur :**
1. Entrez votre nom complet
2. Entrez le nom de la machine (hostname) : `ubuntu-tp`
3. Entrez votre nom d'utilisateur : `etudiant`
4. Créez un mot de passe fort et confirmez-le
5. Cochez "Install OpenSSH server" pour accéder à distance (optionnel)

**Installation :**
- L'installation commence et progresse
- Attendez la fin (généralement 5 à 10 minutes)
- Ne fermez pas la fenêtre

**Redémarrage :**
- Une fois l'installation terminée, cliquez sur "Reboot Now"
- Appuyez sur "Entrée" si demandé

### 4.3 Premier accès à la machine

1. La machine redémarre et affiche l'écran de connexion
2. Entrez votre nom d'utilisateur : `etudiant`
3. Entrez votre mot de passe
4. Vous êtes connecté à Ubuntu Server

## Étape 5 : Vérifications et configuration initiale

### 5.1 Vérifier les informations système

Exécutez les commandes suivantes pour vérifier votre installation :

```bash
# Afficher la version d'Ubuntu
cat /etc/os-release

# Afficher les informations CPU
lscpu

# Afficher l'utilisation de la RAM
free -h

# Afficher l'espace disque
df -h

# Afficher le nom d'hôte
hostname
```

### 5.2 Mettre à jour le système

```bash
# Mettre à jour les listes de paquets
sudo apt update

# Upgrader les paquets installés
sudo apt upgrade -y
```

### 5.3 Installer des outils utiles (optionnel)

```bash
# Installer nano (éditeur de texte)
sudo apt install nano -y

# Installer curl (pour télécharger des fichiers)
sudo apt install curl -y

# Installer git (pour le contrôle de version)
sudo apt install git -y
```

## Étape 6 : Utilisation avancée

### 6.1 Redimensionner la fenêtre

- Allez dans le menu "View" de VirtualBox
- Sélectionnez la résolution souhaitée
- La fenêtre se redimensionnera

### 6.2 Activez le "Seamless Mode" (optionnel)

- Allez dans le menu "View" → "Seamless Mode"
- La machine virtuelle s'intègre à votre bureau hôte

### 6.3 Partage de fichiers

Pour partager des fichiers entre votre machine hôte et la VM :

1. Dans VirtualBox, cliquez sur "Devices" → "Shared Folders" → "Shared Folders Settings"
2. Cliquez sur l'icône "+" pour ajouter un dossier
3. Sélectionnez un dossier de votre machine hôte
4. Donnez un nom au partage (par exemple : `shared`)
5. Cochez "Auto-mount" et "Make Permanent"
6. Cliquez sur "OK"

### 6.4 Créer des points de sauvegarde (Snapshots)

Les snapshots permettent de revenir à un état antérieur de la VM :

```
Étapes :
1. Machine virtuelle arrêtée ou en cours d'exécution
2. Clic droit sur la VM → Snapshots
3. Cliquez sur le bouton "Take Snapshot"
4. Nommez le snapshot (ex. : "Après installation")
5. Confirmez

Pour restaurer :
1. Clic droit sur la VM → Snapshots
2. Sélectionnez le snapshot à restaurer
3. Cliquez sur "Restore"
```

## Questions de contrôle

1. **Quels sont les prérequis matériels minimums pour exécuter VirtualBox ?**

2. **Quelle est la différence entre une allocation de disque "Dynamically allocated" et "Fixed size" ?**

3. **Pourquoi avons-nous alloué 2 GB de RAM à la machine virtuelle ?**

4. **Citez au moins 3 formats de disque virtuel supportés par VirtualBox**

5. **Quel avantage offre une allocation dynamique pour un environnement d'apprentissage ?**

6. **Comment se connecter à distance à la machine virtuelle Ubuntu ?**

7. **Quelle commande permet de mettre à jour tous les paquets d'Ubuntu Server ?**

8. **Quel contrôleur de disque recommandez-vous pour une VM moderne et pourquoi ?**

9. **Quelle est l'utilité des snapshots dans VirtualBox ?**

10. **En quoi le format VMDK est-il avantageux par rapport au VDI ?**

## Dépannage

### La machine virtuelle refuse de démarrer

- Vérifiez que la virtualisation est activée dans le BIOS
- Sur Windows, activez Hyper-V si VirtualBox ne démarre pas
- Réduisez la RAM allouée et réessayez

### L'image ISO n'est pas reconnue

- Vérifiez que le fichier ISO n'est pas corrompu
- Téléchargez à nouveau l'ISO depuis le site officiel
- Vérifiez la somme de contrôle (SHA256)

### La vitesse est très lente

- Augmentez la RAM allouée (passez à 3-4 GB)
- Augmentez le nombre de processeurs alloués
- Changez le mode d'allocation à "Fixed size" pour meilleures performances
- Utilisez "Paravirtualization" dans les paramètres système

### Le disque remplit trop vite

- Avec allocation dynamique, libérez de l'espace dans la VM
- Compactez le disque dynamique via VirtualBox Manager
- Augmentez l'espace disponible sur le disque hôte
- Passez à un disque de taille plus grande si nécessaire

### Écran figé après le démarrage

- Appuyez sur Ctrl+Alt+T pour ouvrir un terminal
- Attendez quelques secondes que le système soit prêt
- Relancez la machine virtuelle

## Conclusion

Vous avez maintenant une machine virtuelle Linux complètement fonctionnelle sur VirtualBox. Cette VM peut être utilisée pour :
- Développer des applications Linux
- Tester des scripts bash
- Apprendre l'administration système
- Expérimenter avec des services (serveur web, base de données, etc.)
- Tester des configurations DevOps et CI/CD

Vous pouvez créer des points de sauvegarde (snapshots) de votre machine pour revenir à un état antérieur si nécessaire. N'hésitez pas à explorer les différentes options de configuration pour adapter votre environnement virtuel à vos besoins spécifiques.