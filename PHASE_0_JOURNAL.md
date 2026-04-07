# 📘 JOURNAL DE BORD : FORTRESSLAB - NEXUS CORP

**Auteur :** Alae-ad  
**Projet :** Construction d'une infrastructure de sécurité (Nexus Corp)  
**Date de début :** 24 Mai 2024  
**Statut actuel :** Phase 0 terminée - Fondations & Linux CLI de base

---

##  LOG 0.1 : Mise en place de l'environnement virtuel (KVM)

###  Objectif
Installer un hyperviseur natif Linux (KVM) et déployer la première machine virtuelle serveur (`Nexus-Core-01`) pour servir de socle à l'infrastructure Nexus Corp.

### ⚙️ Actions Techniques
1.  **Installation des paquets KVM :**
    ```bash
    sudo apt update && sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager ovmf
    ```
2.  **Configuration des permissions :**
    Ajout de l'utilisateur courant au groupe `libvirt` pour éviter l'usage constant de `sudo`.
    ```bash
    sudo usermod -aG libvirt $USER
    ```
    *Note : Nécessite une déconnexion/reconnexion pour生效.*
3.  **Téléchargement de l'ISO :**
    Récupération d'Ubuntu Server 24.04 LTS via `wget` dans le dossier `~/ISOs`.
4.  **Création de la VM via `virt-manager` :**
    *   Nom : `Nexus-Core-01`
    *   RAM : 2 Go | CPU : 2 vCPU | Disque : 20 Go
    *   Réseau : NAT (default)
    *   Installation : Ubuntu Server (Mode Headless, sans interface graphique).
5.  **Configuration durant l'install :**
    *   Utilisateur créé : `alae-ad`
    *   Hostname : `nexus-core`
    *   SSH Server : **Activé** (Critique pour l'accès distant).

### 🛠️ Problèmes Rencontrés & Résolutions
*   **Problème :** Qualité d'affichage médiocre dans la fenêtre Virt-Manager (pixelisation).
    *   **Cause :** Protocole VNC/SPICE non optimisé + absence de pilotes graphiques sur un serveur minimal.
    *   **Solution :** Abandon de l'affichage direct au profit d'une connexion SSH depuis l'hôte. C'est plus réaliste et plus performant.
*   **Problème :** Permission refusée lors de la création de VM après ajout au groupe.
    *   **Cause :** Le groupe n'était pas actif dans la session courante.
    *   **Solution :** Déconnexion et reconnexion complète de la session utilisateur.

### 💡 Réflexion Sécurité (Thinking like an Attacker)
*   **Pourquoi KVM et pas VirtualBox ?** KVM est natif au noyau Linux, utilisé en production (Cloud/Datacenter). Maîtriser KVM, c'est maîtriser l'outil industriel.
*   **Risque de l'ISO :** Si l'ISO était corrompue ou modifiée (supply chain attack), tout le lab serait compromis.
    *   *Contre-mesure future :* Vérifier les checksums SHA256 des ISOs avant installation.

---

## 📅 LOG 0.2 : Prise en main du Terminal & Connexion SSH

### 🎯 Objectif
Maîtriser les bases de la navigation Linux et sécuriser l'accès à distance via SSH (clés cryptographiques).

### ️ Actions Techniques
1.  **Vérification système :**
    *   `whoami` → `alae-ad`
    *   `sudo whoami` → `root` (Validation des droits admin).
    *   `ip addr` → IP détectée : `192.168.122.105`.
    *   `ping google.com` → Connectivité Internet OK.
2.  **Connexion SSH initiale (Mot de passe) :**
    ```bash
    ssh alae-ad@192.168.122.105
    ```
3.  **Génération de clés SSH (Authentification forte) :**
    Sur l'hôte :
    ```bash
    ssh-keygen -t ed25519 -C "alae-ad@nexus-core"
    ssh-copy-id alae-ad@192.168.122.105
    ```
    *Résultat :* Connexion possible sans mot de passe.
4.  **Hardening SSH (Sécurisation) :**
    Modification de `/etc/ssh/sshd_config` sur la VM :
    *   `PasswordAuthentication no` (Désactivation mot de passe)
    *   `PermitRootLogin no` (Interdiction accès root direct)
    *   Redémarrage service : `sudo systemctl restart ssh`

### 🧪 Test de Résilience (Simulation d'incident)
*   **Scénario :** Arrêt du service SSH (`sudo systemctl stop ssh`) alors qu'une session est ouverte.
*   **Observation :** Impossible de se reconnecter depuis une nouvelle fenêtre (`Connection refused`).
*   **Résolution :** Utilisation de la session active pour redémarrer le service (`sudo systemctl start ssh`).
*   **Leçon :** Ne jamais fermer sa session actuelle avant d'avoir validé une nouvelle configuration réseau dans une seconde fenêtre.

### 📂 Exercices Linux Réalisés
*   Navigation : `pwd`, `ls -la`, `cd`, `mkdir`.
*   Manipulation fichiers : `touch`, `echo`, `cat`, `cp`, `mv`, `rm`.
*   Analyse logs : `head`, `tail`, `less`, `grep`.
*   Processus : `ps aux`, `top`.

### 💡 Réflexion Sécurité (Thinking like an Attacker)
*   **Clés SSH vs Mot de passe :** Les mots de passe sont vulnérables au Brute-Force et au Phishing. Les clés SSH (Ed25519) sont mathématiquement beaucoup plus difficiles à craquer et immunisent contre les attaques par dictionnaire.
*   **Permissions `-rwxrwxrwx` (777) :** Très dangereux. Signifie que n'importe quel utilisateur (ou processus compromis) peut lire, modifier et exécuter le fichier. Un attaquant chercherait activement ces fichiers pour injecter du code malveillant ou voler des données sensibles.
*   **Accès Root :** Désactiver `PermitRootLogin` force l'attaquant à compromettre d'abord un utilisateur standard avant d'élever ses privilèges, ajoutant une couche de défense supplémentaire.

---

##  État d'Avancement Global

| Composant | Statut | Notes |
| :--- | :---: | :--- |
| Hyperviseur (KVM) | ✅ Terminé | Configuré et fonctionnel |
| VM Nexus-Core-01 | ✅ Terminé | Ubuntu Server 24.04 installé |
| Réseau VM | ✅ Terminé | IP Fixe/DHCP fonctionnelle, Internet OK |
| Accès SSH | ✅ Sécurisé | Clés Ed25519 configurées, Password Auth désactivé |
| Documentation | ✅ À jour | Journal structuré, commandes notées |
| Mindset Sécurité |  En cours | Réflexes "Attaquant/Défenseur" initiés |

---

## 🚀 Prochaines Étapes (Mission 0.3)
1.  Approfondir la gestion des utilisateurs et groupes (`/etc/passwd`, `/etc/shadow`).
2.  Comprendre et manipuler les permissions Linux (`chmod`, `chown`, SUID).
3.  Automatiser une tâche simple avec un script Bash basique.
4.  Publier ce journal sur GitHub (Premier commit public).

## 📅 LOG 0.3 : Utilisateurs, Groupes et Exploitation SUID (Approfondi)

### 🎯 Objectif
Comprendre la gestion des identités Linux (`/etc/passwd`, `/etc/shadow`) et maîtriser le mécanisme d'élévation de privilèges via le bit **SUID** (Set User ID), en distinguant le comportement des scripts interprétés vs les binaires compilés.

### 🔍 Analyse Système & Concepts Clés
1.  **Séparation des secrets :**
    *   `/etc/passwd` : Liste les utilisateurs (lisible par tous). Le mot de passe est masqué par un `x`.
    *   `/etc/shadow` : Contient les hashs des mots de passe (lisible uniquement par `root`).
    *   *Leçon :* Cette séparation empêche un utilisateur standard de voler les empreintes de mots de passe pour les casser hors ligne.

2.  **Gestion des permissions :**
    *   Structure : `Owner` / `Group` / `Others`.
    *   Droits : `r` (Read), `w` (Write), `x` (Execute).
    *   Commandes : `chmod` (changer les modes), `chown` (changer le propriétaire).

### ️ Lab Pratique : Élévation de Privilèges (Privilege Escalation)

#### Scénario A : Script Bash (Échec attendu sur systèmes modernes)
*   **Action :** Création d'un script `/tmp/malicious_script.sh` avec `#!/bin/bash`, appartenant à `root` avec le bit SUID (`chmod u+s`).
*   **Résultat :** L'exécution par l'utilisateur `hacker-trainee` n'a **PAS** donné les droits root. `whoami` retournait toujours `hacker-trainee`.
*   **Analyse Technique :** Les noyaux Linux modernes (comme celui d'Ubuntu 24.04) ignorent délibérément le bit SUID sur les **scripts interprétés** pour des raisons de sécurité (risque d'injection de variables d'environnement).

#### Scénario B : Binaire Compilé en C (Succès confirmé) ✅
*   **Action :** Compilation d'un petit programme C (`exploit.c`) utilisant `setuid(0)` et `system("/bin/bash")`.
    ```c
    int main(void) { setuid(0); system("/bin/bash"); return 0; }
    ```
*   **Configuration :** Attribution à `root` et activation du SUID : `sudo chmod 4755 /tmp/exploit`.
*   **Exécution :** Lancé par `hacker-trainee`.
*   **Résultat :** `whoami` affiche **`root`**.
*   **Conclusion :** Le bit SUID fonctionne parfaitement sur les **binaires compilés**. C'est le vecteur d'attaque classique pour l'élévation de privilèges.

#### Scénario C : Interpréteur Python (Nuance importante)
*   **Observation :** Un script `.py` direct avec SUID échoue (même raison que le Bash).
*   **Contournement théorique :** Si l'on rend le binaire de l'interpréteur lui-même SUID (ex: `cp /usr/bin/python3 /tmp/my_python` + `chmod u+s`), alors l'exécution du script via ce binaire (`/tmp/my_python script.py`) permet l'élévation.
*   **Leçon :** En audit, il faut chercher non seulement les scripts suspects, mais surtout les binaires légitimes (python, vim, find) mal configurés avec le bit SUID.

### ️ Réflexion Sécurité (Thinking like an Attacker & Defender)
*   **Attaquant :** Cherche systématiquement les fichiers avec le bit `s` (`find / -perm -u=s`). Un binaire éditeur de texte ou un interpréteur avec ce bit est une compromission totale immédiate.
*   **Défenseur (Blue Team) :**
    *   Ne jamais mettre le bit SUID sur des scripts.
    *   Auditer régulièrement les fichiers SUID : `find / -perm -u=s -type f 2>/dev/null`.
    *   Retirer immédiatement le bit SUID (`chmod u-s`) sur tout fichier non essentiel.

###  Nettoyage du Lab
Suppression des fichiers dangereux après validation :
- `sudo rm /tmp/exploit`
- `sudo rm /tmp/malicious_script.sh`
- `sudo rm /tmp/test_suid.py`
- `sudo rm /tmp/my_python`

###  Prochaines Étapes
Passer à l'analyse des logs (`/var/log/auth.log`) pour détecter ces tentatives d'élévation de privilèges (Mission 0.4).
