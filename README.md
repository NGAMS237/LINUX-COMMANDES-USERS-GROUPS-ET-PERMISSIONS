# LINUX-COMMANDES-USERS-GROUPS-ET-PERMISSIONS

# Utilisateurs, groupes, `root` et `sudo` – Linux

Ce dépôt regroupe des notes et des exemples sur les **utilisateurs**, **groupes**, le compte **`root`**, **`sudo`** et les **permissions** sous Linux.  
Ces notions sont essentielles pour la sécurité et l’administration de systèmes (ex. TP de 420‑FBD‑LI).

---

## 1. Utilisateurs et groupes

### 1.1 Rôle d’un utilisateur et d’un groupe

- Chaque **fichier** possède un **propriétaire** (un utilisateur) et appartient à un **groupe**.  
- Les permissions s’appliquent pour trois catégories :
  - **u** : propriétaire (user)  
  - **g** : groupe (group)  
  - **o** : autres utilisateurs (others)  
  - **a** : tous (all)

---

### 1.2 Commandes de base sur les utilisateurs

| Commande | Effet |
|----------|-------|
| `whoami` | affiche l’utilisateur actuel |
| `id` | affiche UID, GID et groupes de l’utilisateur courant |
| `groups` | liste les groupes auxquels appartient l’utilisateur |
| `ls -l fichier` | affiche propriétaire et groupe d’un fichier/dossier |

Exemples :

```bash
whoami
id
groups
ls -l fichier.txt
```

---

## 2. Créer, modifier et supprimer un utilisateur

### 2.1 Créer un utilisateur

| Commande | Effet |
|----------|-------|
| `sudo adduser utilisateur` (ou `useradd` sur certaines distros) | crée un utilisateur interactif avec dossier home |

Exemple :

```bash
sudo adduser demo
```

---

### 2.2 Modifier un utilisateur

| Commande | Effet |
|----------|-------|
| `sudo usermod -s /bin/bash utilisateur` | change le shell de l’utilisateur |
| `sudo usermod -c "Nom complet" utilisateur` | ajoute/édite le commentaire (nom complet) |
| `sudo usermod -g groupe utilisateur` | change le **groupe principal** de l’utilisateur |
| `sudo usermod -G groupe1,groupe2 utilisateur` | ajoute l’utilisateur aux groupes **groupe1**, **groupe2** (groupes secondaires) |
| `sudo usermod -L utilisateur` | bloque le compte (empêche la connexion) |
| `sudo usermod -U utilisateur` | débloque le compte |

---

### 2.3 Supprimer un utilisateur

| Commande | Effet |
|----------|-------|
| `sudo deluser utilisateur` | supprime l’utilisateur (mais pas toujours le dossier home) |
| `sudo deluser --remove-home utilisateur` | supprime l’utilisateur **et** son dossier home |
| `sudo userdel utilisateur` (souvent utilisé sur Debian/Ubuntu) | supprime l’utilisateur |

---

## 3. Gestion des groupes

### 3.1 Créer, modifier et supprimer un groupe

| Commande | Effet |
|----------|-------|
| `sudo addgroup nomGroupe` | crée un nouveau groupe |
| `sudo groupmod -n nouveauNom ancienNom` | renomme un groupe |
| `sudo delgroup nomGroupe` | supprime un groupe (si personne n’y appartient) |

---

### 3.2 Gérer les membres d’un groupe

| Commande | Effet |
|----------|-------|
| `sudo usermod -aG groupe utilisateur` | ajoute l’utilisateur au **groupe** (sans supprimer ses autres groupes) |
| `groups utilisateur` | liste les groupes de l’utilisateur |
| `getent group groupe` | affiche le contenu du groupe (membres) |

---

## 4. Le compte `root` et la sécurité

### 4.1 Rôle de `root`

- `root` est le **super‑utilisateur** (UID 0) avec **tous les droits** sur le système.  
- Il peut lire, modifier ou supprimer n’importe quel fichier, passer des commandes systèmes sensibles, etc.  
- **Travailler en permanence en `root` est déconseillé** (risque de commandes accidentelles, `rm -rf /`, `chmod -R 777`, etc.).

---

### 4.2 Se connecter ou passer en `root`

| Commande | Effet |
|----------|-------|
| `su -` | se connecte en tant que `root` (demande le mot de passe `root`) |
| `sudo -i` | ouvre un shell avec les droits `root` (demande votre mot de passe, pas celui de `root`) |
| `sudo -u utilisateur commande` | exécute `commande` avec l’identité de `utilisateur` |

---

## 5. `sudo` – exécuter des commandes avec privilèges

### 5.1 Rôle de `sudo`

- `sudo` permet à certains utilisateurs d’exécuter **des commandes requérant les droits `root`** sans devoir se connecter en `root`.  
- La gestion des droits `sudo` se fait via le fichier `/etc/sudoers` (édité avec `visudo`).

| Commande | Effet |
|----------|-------|
| `sudo commande` | exécute `commande` avec les droits `root` |
| `sudo -l` | liste les commandes que l’utilisateur peut exécuter avec `sudo` |
| `visudo` | ouvre `/etc/sudoers` de façon sécurisée pour ajouter ou modifier des droits |

---

## 6. Permissions – notation symbolique

La **notation symbolique** utilise `r`, `w`, `x` et les opérateurs `+`, `-`, `=`.

### 6.1 Syntaxe générale

```bash
chmod [u/g/o/a][+/-/=][r/w/x] fichier
```

- `u` : propriétaire  
- `g` : groupe  
- `o` : autres  
- `a` : tous  

### 6.2 Exemples courants

| Commande | Effet |
|----------|-------|
| `chmod u=rwx fichier` | propriétaire = `rwx` |
| `chmod u+x,g=r fichier` | propriétaire ajoute `x`; groupe = `r` |
| `chmod go-w fichier` | groupe et autres perdent l’écriture |
| `chmod a-x fichier` | tous perdent l’exécution |

Exemple correspondant à ton TP :

```bash
chmod u=rwx,g=r,o=x fichierTest1
```

- `u=rwx` → propriétaire a tous les droits  
- `g=r` → groupe a lecture seulement  
- `o=x` → autres ont exécution seulement  

---

## 7. Permissions – notation octale

La notation **octale** exprime les permissions sous forme de chiffres :

- `r` = 4  
- `w` = 2  
- `x` = 1  

On additionne les valeurs pour chaque catégorie : `u`, `g`, `o`.

### 7.1 Tableau de base

| Symbole | Valeur |
|---------|--------|
| `r`     | 4      |
| `w`     | 2      |
| `x`     | 1      |

Exemples :

- `rwx` → 4+2+1 = **7**  
- `r-x` → 4+0+1 = **5**  
- `r--` → 4+0+0 = **4**  

### 7.2 Exemples de modes courants

| Mode | Signification (symbole) | Usage |
|------|-------------------------|-------|
| `644` | `rw-r--r--` | fichier standard (propriétaire rw, autres r) |
| `640` | `rw-r-----` | fichier privé (propriétaire rw, groupe r) |
| `755` | `rwxr-xr-x` | script ou dossier public |
| `700` | `rwx------` | très privé (seulement le propriétaire) |
| `777` | `rwxrwxrwx` | ouvert à tous – **à éviter sauf cas très spécifiques** |

Commandes :

```bash
chmod 644 fichier.txt
chmod 755 script.sh
chmod 700 dossierPrivé
```

---

## 8. Changer le propriétaire et le groupe (`chown` / `chgrp`)

### 8.1 `chown` – changer le propriétaire et le groupe

| Commande | Effet |
|----------|-------|
| `chown utilisateur fichier` | change le **propriétaire** seulement |
| `chown utilisateur:groupe fichier` | change **propriétaire et groupe** |
| `chown -R utilisateur: /dossier` | récursif (dossier et sous‑dossiers) |

Exemples :

```bash
chown marc script.sh
chown marc:devs fichier.txt
chown -R marc:devs /home/marc/projet
```

### 8.2 `chgrp` – changer uniquement le groupe

| Commande | Effet |
|----------|-------|
| `chgrp groupe fichier` | change le groupe du fichier |
| `chgrp -R groupe /dossier` | change le groupe de tout le dossier et sous‑dossiers |

Exemple :

```bash
chgrp devs projet/
```

---

## 9. Combinaisons pratiques (exercices de révision)

Utilise ces commandes pour vérifier utilisation, permissions et droits :

```bash
whoami
id
groups
ls -l fichier
stat -c "%a %A %n" fichier      # permissions octales + symbole + nom
```

Exemple de manipulation complète :

```bash
sudo adduser testuser
sudo addgroup devteam
sudo usermod -aG devteam testuser
sudo chown testuser:devteam /home/testuser/projet
chmod 770 /home/testuser/projet
```

---

## 10. Bonnes pratiques

- Ne pas travailler en permanence en `root`.  
- Utiliser `sudo` pour les commandes ponctuelles sensibles.  
- Éviter `chmod -R 777` sur des dossiers système.  
- Standardiser les permissions (`644` pour fichiers, `755` pour scripts, `700` pour privés).  

---

> **Projet :** 420-FBD-LI – Soutien technique et formation  
> **Étudiant :** [Ton nom] – Cégep de Limoilou Cégep de Limoilou
