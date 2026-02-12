# Travaux Pratiques 2 : Virtualisation Complète - Gestion Avancée

## Objectifs

À la fin de ce TP, vous serez capable de :
- Configurer et gérer les réseaux virtuels dans VirtualBox et VMware
- Ajouter et supprimer du matériel (CPU, RAM, disque) à une VM en cours d'exécution
- Exporter et importer des machines virtuelles
- Créer et gérer des snapshots (points de sauvegarde)
- Migrer des machines virtuelles entre hyperviseurs
- Configurer les paramètres avancés de virtualisation

## Prérequis

- TP1 complété avec une VM Ubuntu Server fonctionnelle
- VirtualBox installé et configuré
- Accès administrateur sur la machine hôte
- Au minimum 8 GB de RAM disponible
- Au minimum 50 GB d'espace disque libre
- Connaissance basique de l'administration Linux

## Partie 1 : Gestion des interfaces réseau

### 1.1 Comprendre les modes réseau dans VirtualBox

#### **Mode NAT (Network Address Translation)**

**Concept :**
- La VM accède à Internet via la machine hôte
- L'hôte agit comme un routeur/firewall
- Les connexions entrantes vers la VM ne sont pas possibles directement

**Configuration :**
1. Sélectionnez votre VM → Clic droit → Settings
2. Network → Adapter 1
3. Attaché à : NAT

**Cas d'usage :**
- Accès à Internet pour la VM
- Environnements de test isolés
- Quand la sécurité est importante

**Avantages :**
- Simple à configurer
- Isolement automatique
- Pas de configuration réseau complexe

**Inconvénients :**
- Performance réduite pour les connexions réseau
- Impossible d'accéder à la VM depuis d'autres machines
- Pas de communication directe entre VM sur le même réseau

#### **Mode Pont (Bridged)**

**Concept :**
- La VM fait partie du même réseau que l'hôte
- La VM obtient sa propre adresse IP
- La VM est visible sur le réseau physique

**Configuration :**
1. Sélectionnez votre VM → Clic droit → Settings
2. Network → Adapter 1
3. Attaché à : Bridged Adapter
4. Sélectionnez l'interface réseau (eth0, Wi-Fi, etc.)

**Cas d'usage :**
- VM nécessitant une adresse IP du réseau
- Tests de services réseau
- VM devant être accessibles depuis d'autres machines

**Avantages :**
- Accès direct au réseau physique
- Performance réseau optimale
- Autres machines peuvent accéder à la VM

**Inconvénients :**
- Configuration réseau plus complexe
- Dépend de la disponibilité du réseau hôte
- Peut nécessiter une adresse MAC unique

#### **Mode Réseau Interne (Internal Network)**

**Concept :**
- Les VM sont isolées du réseau hôte et d'Internet
- Les VM peuvent communiquer entre elles via le réseau interne
- Réseau complètement contrôlé

**Configuration :**
1. Sélectionnez votre VM → Clic droit → Settings
2. Network → Adapter 1
3. Attaché à : Internal Network
4. Nommez le réseau (ex : "internalnet")

**Cas d'usage :**
- Créer un réseau de laboratoire isolé
- Tests de clustering
- Environnements de production simulés

**Avantages :**
- Isolement complet
- Contrôle total du réseau
- Parfait pour les labs multi-VM

**Inconvénients :**
- Pas d'accès à Internet
- Configuration DNS et routage nécessaire
- Plus complexe à déboguer

#### **Mode Adaptateur Hôte (Host-only)**

**Concept :**
- VM communique uniquement avec l'hôte
- VM ne peut pas accéder à Internet ou au réseau physique
- Connexion virtuelle VM-Hôte uniquement

**Configuration :**
1. Sélectionnez votre VM → Clic droit → Settings
2. Network → Adapter 1
3. Attaché à : Host-only Adapter
4. Sélectionnez l'adaptateur (vboxnet0, vboxnet1, etc.)

**Cas d'usage :**
- Développement local
- Environnements de test isolés
- Connexions sécurisées VM-Hôte uniquement

**Avantages :**
- Sécurité maximale
- Aucun risque d'exposition réseau
- Facile à configurer

**Inconvénients :**
- Pas d'accès à Internet
- Communication limitée
- Non viable pour les tests réseau

### 1.2 Tableau comparatif des modes réseau

| Mode | Internet | Réseau physique | VM-VM | Hôte-VM | Sécurité |
|------|----------|-----------------|-------|---------|----------|
| NAT | ✓ | ✗ | ✗ | ✓ | Élevée |
| Pont | ✓ | ✓ | ✓ | ✓ | Basse |
| Interne | ✗ | ✗ | ✓ | ✗ | Élevée |
| Host-only | ✗ | ✗ | ✗ | ✓ | Élevée |

### 1.3 Configuration pratique des interfaces réseau

**Objectif : Configurer une VM avec 2 interfaces réseau**

**Étapes :**

1. Arrêtez la machine virtuelle
2. Clic droit sur la VM → Settings
3. Network → Adapter 1 : Configurez NAT (Internet)
4. Network → Adapter 2 : Cochez "Enable Network Adapter"
   - Attaché à : Host-only Adapter
   - Sélectionnez vboxnet0
5. Cliquez OK

**Vérification dans la VM :**

```bash
# Afficher toutes les interfaces réseau
ip link show

# Afficher les adresses IP
ip addr show

# Tester la connectivité Internet
ping 8.8.8.8

# Tester la connectivité vers l'hôte
ping 192.168.56.1  # Adresse par défaut du host-only
```

### 1.4 Configuration avancée du réseau dans VMware

Pour VMware Workstation/Fusion, les concepts sont similaires :

**Fichier de configuration réseau :**
```
Sur Linux/Mac : /etc/vmware/networking
Sur Windows : C:\ProgramData\VMware\hostd\config
```

**Mode Virtual Switch :**
```bash
# Lister les switchs virtuels
vmware-cmd -l

# Créer un switch virtuel
vmware-vswitch
```

## Partie 2 : Gestion du matériel

### 2.1 Ajouter de la RAM à une VM en cours d'exécution

**VirtualBox :**
- Arrêtez d'abord la machine
- Settings → System → Base Memory
- Augmentez la valeur (ex : 2048 MB → 4096 MB)
- Cliquez OK et redémarrez la VM

**Vérification :**
```bash
# Afficher la RAM disponible
free -h

# Afficher les informations détaillées
grep MemTotal /proc/meminfo
```

**Note :** VMware supporte l'ajout de RAM sans arrêt (avec VMware Tools installé)

### 2.2 Ajouter des processeurs (CPU) à une VM

**VirtualBox :**
1. Arrêtez la machine
2. Settings → System → Processors
3. Augmentez le nombre (ex : 2 → 4)
4. Cliquez OK et redémarrez

**Dans la VM :**
```bash
# Vérifier le nombre de CPU
lscpu | grep "CPU(s):"

# Afficher les détails CPU
cat /proc/cpuinfo | head -20

# Monitorer l'utilisation CPU en temps réel
top  # Appuyez sur Q pour quitter
```

### 2.3 Ajouter un disque dur supplémentaire

**VirtualBox - Créer un nouveau disque :**

1. Clic droit sur la VM → Settings
2. Storage → Controller SATA → Cliquez sur l'icône "+" (Add Hard Disk)
3. Sélectionnez "Create New Disk"
4. Configurez :
   - Type : VDI
   - Allocation : Dynamique
   - Taille : 10 GB
5. Donnez un nom au disque
6. Cliquez Finish

**Dans la VM - Partitionner et formater le nouveau disque :**

```bash
# Lister les disques disponibles
sudo lsblk

# Créer une partition (par exemple, /dev/sdb)
sudo fdisk /dev/sdb

# Commandes fdisk :
# m : affiche le menu
# n : crée une nouvelle partition
# p : sélectionne type primaire
# 1 : numéro de partition
# Appuyez sur Entrée deux fois pour utiliser tout l'espace
# w : écrit et quitte

# Vérifier la partition créée
sudo lsblk

# Formater la partition en ext4
sudo mkfs.ext4 /dev/sdb1

# Créer un point de montage
sudo mkdir /mnt/newdisk

# Monter le disque
sudo mount /dev/sdb1 /mnt/newdisk

# Vérifier le montage
df -h | grep newdisk

# Changer la permission (optionnel)
sudo chown -R $USER:$USER /mnt/newdisk
```

**Pour monter automatiquement au démarrage :**

```bash
# Obtenir l'UUID du disque
sudo blkid /dev/sdb1

# Éditer le fichier fstab
sudo nano /etc/fstab

# Ajouter une ligne à la fin :
# UUID=votre-uuid /mnt/newdisk ext4 defaults 0 2

# Vérifier la configuration
sudo mount -a
```

### 2.4 Augmenter la taille d'un disque existant

**VirtualBox - Augmenter le disque :**

```bash
# Sur l'hôte, augmenter le disque VDI
VBoxManage modifyhd "chemin/vers/disque.vdi" --resize 30000  # En MB

# Exemple :
VBoxManage modifyhd "~/VirtualBox VMs/Ubuntu-Server-TP/Ubuntu-Server-TP.vdi" --resize 30000
```

**Dans la VM - Redimensionner la partition :**

```bash
# Afficher les partitions actuelles
sudo parted -l

# Utiliser parted pour redimensionner (VM doit être éteinte pour /dev/sda)
# Ou utiliser gparted (interface graphique)

# Alternative simple avec fdisk + resize2fs :
sudo fdisk /dev/sda

# Supprimer et recréer la partition :
# d : supprimer
# n : créer nouvelle partition
# Utiliser le même début, mais un end différent (plus grand)
# w : écrire

# Redimensionner le système de fichiers
sudo resize2fs /dev/sda1

# Vérifier
df -h
```

### 2.5 Supprimer un disque de la VM

**VirtualBox :**

1. Clic droit sur la VM → Settings
2. Storage → Sélectionnez le disque à supprimer
3. Cliquez sur l'icône "-" (Remove)
4. Confirmez la suppression
5. Cliquez OK

**Important :** Si le disque contient des données, faites d'abord une sauvegarde.

## Partie 3 : Export et Import de machines virtuelles

### 3.1 Export d'une VM en format OVA/OVF

**VirtualBox :**

**Étapes :**
1. Clic droit sur la VM → Export Appliance
2. Format : Sélectionnez "Open Virtualization Format 2.0" (OVF)
3. Fichier de destination : Choisissez un emplacement (ex : ~/exported_vms/)
4. Nom du fichier : `Ubuntu-Server-Export.ova`
5. Cliquez Next
6. Configurez les options d'export :
   - Cochez "Include ISO image files" si vous avez des ISO
   - Sélectionnez le format d'export
7. Cliquez Export

**Temps d'export :** Varie selon la taille du disque (10-30 min pour 20 GB)

**Vérification :**
```bash
# Sur l'hôte, vérifier le fichier créé
ls -lh ~/exported_vms/Ubuntu-Server-Export.ova
```

### 3.2 Import d'une VM depuis un fichier OVA/OVF

**VirtualBox :**

**Étapes :**
1. Menu : File → Import Appliance
2. Sélectionnez le fichier .ova ou .ovf
3. Cliquez Import
4. Configurez les paramètres d'import :
   - Modifiez le nom de la VM si souhaité
   - Ajustez la RAM si nécessaire
   - Sélectionnez le contrôleur de stockage
5. Cliquez Import

**Résultat :** Une nouvelle VM est créée avec la même configuration

### 3.3 Clonage d'une VM

**Alternative plus rapide à l'export/import :**

**VirtualBox :**

1. Arrêtez la VM source
2. Clic droit sur la VM → Clone
3. Configurez :
   - Nouveau nom : `Ubuntu-Server-Clone`
   - Type de clone : "Full Clone" (copie complète)
   - Cochez "Reinitialize the MAC address of all network cards"
4. Cliquez Clone

**Avantages du clonage :**
- Plus rapide que export/import
- Les paramètres sont préservés
- Idéal pour créer des environnements de test

### 3.4 Sauvegarde et restauration de configuration

**Exporter la configuration XML :**

```bash
# VirtualBox stocke les configurations en XML
# Emplacement :
# Linux : ~/.config/VirtualBox/VirtualBox.xml
# Windows : %APPDATA%\VirtualBox\VirtualBox.xml
# macOS : ~/Library/VirtualBox/VirtualBox.xml

# Sauvegarder la configuration
cp ~/.config/VirtualBox/VirtualBox.xml ~/backup/VirtualBox_backup.xml
```

## Partie 4 : Snapshots et restauration

### 4.1 Comprendre les snapshots

**Concept :**
- Un snapshot est une photo instantanée de l'état complet d'une VM
- Inclueur l'état du disque, la RAM, les paramètres
- Permet de revenir rapidement à cet état

**Types de snapshots :**

- **Snapshot à chaud** : VM en exécution
- **Snapshot à froid** : VM arrêtée (recommandé)

### 4.2 Créer un snapshot

**VirtualBox :**

**Via l'interface graphique :**
1. Sélectionnez la VM
2. Clic droit → Snapshots
3. Cliquez sur l'icône "Camera" ou "Take Snapshot"
4. Nommez le snapshot (ex : "Après installation de Docker")
5. Ajoutez une description (optionnel)
6. Cliquez OK

**Via la ligne de commande :**
```bash
# Créer un snapshot
VBoxManage snapshot "Ubuntu-Server-TP" take "Snapshot-Avant-Test" --description "État avant test de déploiement"

# Lister les snapshots
VBoxManage snapshot "Ubuntu-Server-TP" list

# Afficher les détails d'un snapshot
VBoxManage snapshot "Ubuntu-Server-TP" list --machinereadable
```

### 4.3 Restaurer depuis un snapshot

**Via l'interface graphique :**

1. Clic droit sur la VM → Snapshots
2. Sélectionnez le snapshot à restaurer
3. Clic droit sur le snapshot → Restore
4. Confirmez la restauration
5. La VM redémarre avec l'état du snapshot

**Via la ligne de commande :**
```bash
# Restaurer un snapshot
VBoxManage snapshot "Ubuntu-Server-TP" restore "Snapshot-Avant-Test"

# Restaurer l'état actuel (avant modification)
VBoxManage snapshot "Ubuntu-Server-TP" restorecurrent
```

### 4.4 Supprimer un snapshot

**Important :** Supprimer un snapshot consolide les modifications au snapshot précédent.

```bash
# Via ligne de commande :
VBoxManage snapshot "Ubuntu-Server-TP" delete "Snapshot-Avant-Test"

# Via l'interface :
1. Clic droit sur la VM → Snapshots
2. Sélectionnez le snapshot
3. Clic droit → Delete
4. Confirmez
```

### 4.5 Hiérarchie des snapshots

**Structure :**
```
État actuel
    ↑
    ├─ Snapshot 1 (Installation)
    │   └─ Snapshot 1.1 (Après update)
    │       └─ Snapshot 1.1.1 (Avec Docker)
    └─ Snapshot 2 (Configuration alternative)
```

**Gestion des branches :**
```bash
# Afficher la hiérarchie complète
VBoxManage snapshot "Ubuntu-Server-TP" list --machinereadable | grep UUID

# Restaurer crée une nouvelle branche
# L'ancienne branche reste accessible
```

## Partie 5 : Migration de machines virtuelles

### 5.1 Migration intra-hyperviseur (VirtualBox)

**Copier une VM vers une autre location :**

```bash
# Arrêtez la VM
# Naviguer vers le dossier des VMs
cd ~/VirtualBox\ VMs/

# Copier le dossier complet
cp -r "Ubuntu-Server-TP" "Ubuntu-Server-TP-Backup"

# Dans VirtualBox :
# 1. File → Add → Sélectionnez le fichier .vbox dans le nouveau dossier
# 2. La VM est importée
```

### 5.2 Migration VirtualBox → VMware

**Étape 1 : Exporter depuis VirtualBox en OVA**

```bash
VBoxManage export "Ubuntu-Server-TP" -o ~/exported_vms/ubuntu-to-vmware.ova
```

**Étape 2 : Importer dans VMware**

```bash
# VMware Workstation (Linux/Windows)
vmware -o ~/VMs/ ~/exported_vms/ubuntu-to-vmware.ova

# Alternative manuelle :
# 1. Ouvrez VMware Workstation
# 2. File → Open → Sélectionnez le fichier .ova
# 3. Configurez les paramètres
# 4. Cliquez Import
```

**Étape 3 : Installer VMware Tools**

```bash
# Dans la VM VMware :

# Pour Ubuntu/Debian :
sudo apt update
sudo apt install open-vm-tools -y
sudo apt install open-vm-tools-desktop -y

# Redémarrer
sudo reboot

# Vérifier l'installation
sudo systemctl status open-vm-tools
```

### 5.3 Migration VirtualBox → Hyper-V

**VirtualBox exporte en VHDX (Hyper-V):**

```bash
# Exporter la VM en format VHDX (depuis Windows)
VBoxManage export "Ubuntu-Server-TP" -o C:\temp\ubuntu-export.vhdx --format vhdx
```

**Importer dans Hyper-V :**

```powershell
# PowerShell (en tant qu'administrateur)

# Créer un nouveau dossier pour la VM
New-Item -Path "C:\Hyper-V\VMs\Ubuntu-Imported" -ItemType Directory

# Importer le disque VHDX
Copy-Item "C:\temp\ubuntu-export.vhdx" "C:\Hyper-V\VMs\Ubuntu-Imported\disk.vhdx"

# Créer la VM Hyper-V
New-VM -Name "Ubuntu-Imported" `
  -MemoryStartupBytes 2GB `
  -VHDPath "C:\Hyper-V\VMs\Ubuntu-Imported\disk.vhdx" `
  -Generation 2

# Démarrer la VM
Start-VM -Name "Ubuntu-Imported"
```

### 5.4 Vérifier la compatibilité après migration

**Une fois la VM migrée :**

```bash
# Vérifier l'accès réseau
ping 8.8.8.8

# Vérifier les ressources allouées
free -h
lscpu

# Vérifier l'espace disque
df -h

# Vérifier les interfaces réseau
ip addr show

# Vérifier les logs système
sudo dmesg | tail -20

# Pour VMware : vérifier les VMware Tools
sudo systemctl status open-vm-tools

# Pour Hyper-V : vérifier Hyper-V Integration Services
# (Automatique si Linux Integration Services installés)
```

## Partie 6 : Configuration avancée

### 6.1 Accélération matérielle

**VirtualBox :**

```bash
# Vérifier le support de la virtualisation
grep -E "svm|vmx" /proc/cpuinfo

# Activer l'accélération 3D
1. Settings → Display → Enable 3D Acceleration (cochez)

# Accélération vidéo
Settings → Display → Video Memory : 128 MB (max)
```

**Configuration BIOS/UEFI :**
- Activez "Virtualization Technology" dans le BIOS
- Sur Intel : VT-x
- Sur AMD : AMD-V

### 6.2 Allocation dynamique de ressources

**VirtualBox :**
- RAM dynamique : Non supporté nativement
- CPU dynamique : Non supporté nativement
- Disque dynamique : Supporté (configuré dans ce TP)

### 6.3 Configuration des paramètres avancés

**Désactiver les USB :**
```
Settings → USB → Décochez "Enable USB Controller"
```

**Désactiver l'audio :**
```
Settings → Audio → Décochez "Enable Audio"
```

**Définir la priorité du processus :**
```bash
# Sur l'hôte Linux :
ps aux | grep VirtualBox  # Trouver le PID
renice -20 -p <PID>  # Augmenter la priorité
```

## Partie 7 : Exercices pratiques

### Exercice 1 : Configuration réseau multicouche

**Objectif :** Configurer 3 VM sur un réseau interne avec accès Internet

**Étapes :**
1. Créez 3 clones de votre VM (VM1, VM2, VM3)
2. VM1 : NAT + Host-only
3. VM2 : Réseau interne uniquement
4. VM3 : Réseau interne uniquement
5. Configurez le routage sur VM1 pour partager Internet à VM2/VM3

**Vérification :**
```bash
# Sur VM2 et VM3 :
ping VM1-hôte-only-IP  # Doit fonctionner
ping 8.8.8.8  # Doit fonctionner si routage configuré
```

### Exercice 2 : Snapshot et récupération d'erreur

**Objectif :** Pratiquer la création/restauration de snapshots

**Étapes :**
1. Créez un snapshot "Clean Install"
2. Installez des packages : `sudo apt install nginx -y`
3. Créez un second snapshot "With Nginx"
4. Cassez le système intentionnellement : `sudo rm -rf /etc/nginx`
5. Restaurez le snapshot "With Nginx"
6. Vérifiez que nginx fonctionne à nouveau

### Exercice 3 : Clone et comparaison

**Objectif :** Cloner une VM et effectuer des modifications

**Étapes :**
1. Clonez votre VM → "Ubuntu-Test"
2. Sur Ubuntu-Test : installez Docker
3. Sur Ubuntu-Test : changez le hostname
4. Exportez Ubuntu-Test en OVA
5. Importez l'OVA comme nouvelle VM "Ubuntu-Test-Imported"
6. Vérifiez que Docker est installé et le hostname préservé

## Questions de révision

1. **Quels sont les 4 modes réseau principaux de VirtualBox ?**

2. **Quand recommanderiez-vous le mode Pont plutôt que NAT ?**

3. **Comment ajouter 10 GB à un disque VirtualBox existant ?**

4. **Quels outils utiliseriez-vous pour vérifier que les ressources ont été ajoutées à une VM Linux ?**

5. **Quelle est la différence entre cloner une VM et l'exporter ?**

6. **Comment créer et restaurer un snapshot via CLI ?**

7. **Pourquoi est-il important de réinitialiser l'adresse MAC lors du clonage ?**

8. **Quels fichiers formater pour la migration cross-hyperviseur ?**

9. **Comment vérifier que la virtualisation est activée au niveau CPU ?**

10. **Expliquez la hiérarchie des snapshots et comment elle fonctionne.**

## Dépannage avancé

### Problème : La VM ne voit pas les nouvelles ressources

```bash
# Solutions :
1. Redémarrez la VM après ajout de ressources
2. Vérifiez avec : free -h, lscpu, df -h
3. Pour le disque : vérifiez avec sudo lsblk
4. Relancez VirtualBox si rien ne change
```

### Problème : Le réseau interne ne fonctionne pas

```bash
# Vérifiez la configuration :
1. Les VM ont le même nom de réseau interne
2. Les interfaces sont activées (ip link show)
3. Pas de firewall bloquant
4. Vérifiez avec : ping 192.168.x.x
```

### Problème : L'import OVA échoue

```bash
# Solutions :
1. Vérifiez l'intégrité du fichier OVA
2. Assurez-vous d'avoir assez d'espace disque
3. Fermez VirtualBox et rouvrez
4. Essayez depuis la ligne de commande :
   VBoxManage import chemin/vers/fichier.ova
```

### Problème : Les snapshots ralentissent énormément

```bash
# Solutions :
1. Supprimez les anciens snapshots inutiles
2. Consolidez les snapshots
3. Lisez les snapshots orphelins

# Commande VBoxManage :
VBoxManage snapshot "VM-Name" list

# Supprimez les inutiles
VBoxManage snapshot "VM-Name" delete "Old-Snapshot"
```

## Conclusion

Vous maîtrisez maintenant la virtualisation complète :
- ✓ Configuration réseau avancée
- ✓ Gestion flexible du matériel
- ✓ Export/Import cross-plateformes
- ✓ Snapshots et récupération
- ✓ Migration entre hyperviseurs
- ✓ Configuration optimale des performances

Ces compétences sont essentielles pour l'administration infrastructure, le DevOps, et l'architecture cloud.

## Ressources supplémentaires

- Documentation VirtualBox : https://www.virtualbox.org/manual/
- Documentation VMware : https://docs.vmware.com/
- Hyper-V : https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/
- KVM/QEMU : https://www.linux-kvm.org/
- OpenStack : https://www.openstack.org/
