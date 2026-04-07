# 🛠️ Cheat Sheet : Commandes Linux & Git Essentielles
*Projet : FortressLab - Nexus Corp*
*Dernière mise à jour : Phase 0*

Ce document recense les commandes critiques maîtrisées durant la Phase 0. Il sert de référence rapide pour l'administration système et la sécurité.

---

## 📂 1. Navigation & Système
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `pwd` | Affiche le chemin absolu actuel | `pwd` → `/home/alae-ad` |
| `ls -la` | Liste tous les fichiers (cachés + détails) | `ls -la /etc` |
| `cd [dossier]` | Change de répertoire | `cd /var/log` |
| `cd ~` | Retourne au dossier personnel | `cd ~` |
| `whoami` | Affiche l'utilisateur courant | `whoami` |
| `id` | Affiche UID, GID et groupes | `id alae-ad` |
| `uname -a` | Infos complètes du noyau (Kernel) | `uname -a` |
| `hostname` | Nom de la machine | `hostname` |

##  2. Manipulation de Fichiers
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `touch [fichier]` | Crée un fichier vide | `touch test.txt` |
| `mkdir [dossier]` | Crée un dossier | `mkdir lab` |
| `cat [fichier]` | Affiche le contenu entier | `cat notes.txt` |
| `head [fichier]` | Affiche les 10 premières lignes | `head syslog` |
| `tail [fichier]` | Affiche les 10 dernières lignes | `tail -f auth.log` (suivi temps réel) |
| `less [fichier]` | Navigation paginée dans un fichier | `less huge_log.txt` (q pour quitter) |
| `cp [src] [dest]` | Copie un fichier | `cp file.txt backup.txt` |
| `mv [src] [dest]` | Déplace ou renomme | `mv old.txt new.txt` |
| `rm [fichier]` | Supprime définitivement ⚠️ | `rm temp.txt` |
| `nano [fichier]` | Éditeur de texte simple | `nano config.conf` |

## 🔍 3. Recherche & Analyse
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `grep "[motif]" [fichier]` | Cherche du texte dans un fichier | `grep "error" syslog` |
| `find [chemin] -name "[nom]"` | Trouve des fichiers par nom | `find / -name "*.conf"` |
| `find / -perm -u=s` | **CRITIQUE :** Trouve tous les fichiers SUID | Audit de sécurité |
| `which [cmd]` | Trouve le chemin d'une commande | `which python3` |
| `history` | Historique des commandes tapées | `history \| grep ssh` |

## 🔐 4. Utilisateurs & Permissions (Sécurité)
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `sudo [cmd]` | Exécute en tant que root/admin | `sudo apt update` |
| `su - [user]` | Change d'utilisateur (switch user) | `su - hacker-trainee` |
| `adduser [nom]` | Crée un nouvel utilisateur | `sudo adduser bob` |
| `passwd` | Change son propre mot de passe | `passwd` |
| `chmod [mode] [fichier]` | Change les permissions (rwx) | `chmod 755 script.sh` |
| `chmod u+s [fichier]` | Active le bit SUID (Danger!) | `sudo chmod u+s exploit` |
| `chmod u-s [fichier]` | Désactive le bit SUID (Sécurisation) | `sudo chmod u-s exploit` |
| `chown [user]:[group] [f]` | Change propriétaire/groupe | `sudo chown root:root file` |
| `cat /etc/passwd` | Liste des utilisateurs (public) | `cat /etc/passwd` |
| `sudo cat /etc/shadow` | Liste des hashs mots de passe (secret) | `sudo cat /etc/shadow` |

##  5. Réseau & SSH
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `ip addr` | Affiche les interfaces et IP | `ip addr` |
| `ping [cible]` | Test de connectivité | `ping google.com` |
| `ssh [user]@[ip]` | Connexion distante sécurisée | `ssh alae-ad@192.168.1.10` |
| `ssh-keygen -t ed25519` | Génère une clé SSH forte | `ssh-keygen -t ed25519` |
| `ssh-copy-id [user]@[ip]` | Copie la clé publique sur le serveur | `ssh-copy-id alae-ad@192.168.1.10` |
| `systemctl status [service]` | Vérifie l'état d'un service | `systemctl status ssh` |
| `systemctl restart [service]` | Redémarre un service | `sudo systemctl restart ssh` |

## 📦 6. Gestion des Paquets (Ubuntu/Debian)
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `apt update` | Met à jour la liste des paquets | `sudo apt update` |
| `apt install [paquet]` | Installe un logiciel | `sudo apt install git` |
| `apt upgrade` | Met à jour les logiciels installés | `sudo apt upgrade` |
| `wget [url]` | Télécharge un fichier | `wget http://site.com/file.iso` |

## 🔄 7. Git (Versionning)
| Commande | Description | Exemple |
| :--- | :--- | :--- |
| `git init` | Initialise un dépôt local | `git init` |
| `git status` | Voir l'état des modifications | `git status` |
| `git add .` | Ajoute tout au staging | `git add .` |
| `git commit -m "msg"` | Enregistre les changements | `git commit -m "update docs"` |
| `git push` | Envoie vers GitHub | `git push` |
| `git remote add origin [url]` | Lie le repo local à GitHub | `git remote add origin ...` |

---
### 💡 Astuces Mémo
- **Tabulation :** Appuie sur `Tab` pour auto-compléter les noms de fichiers ou commandes.
- **Historique :** Flèche haut/bas pour naviguer dans les commandes précédentes.
- **Annuler :** `Ctrl+C` arrête une commande en cours d'exécution.
- **Effacer ligne :** `Ctrl+U` efface toute la ligne actuelle dans le terminal.
- **Manuel :** `man [commande]` affiche le manuel complet (ex: `man ls`).
