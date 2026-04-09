# LINUX-COMMANDES-USERS-GROUPS-ET-PERMISSIONS



# Utilisateurs, groupes et permissions sous Linux

Ce dépôt regroupe des notes et des exemples sur les **utilisateurs**, **groupes**, le compte **`root`** et les **permissions** Linux (en mode **octal** et **symbolique**).  
Ces notions sont fondamentales pour la sécurité et l’administration de systèmes Linux (ex. TP de 420‑FBD‑LI).

---

## 1. Utilisateurs et groupes

### 1.1 Principe basique

Sous Linux, chaque fichier possède un **propriétaire** (un utilisateur) et appartient à un **groupe**.  
Les permissions sont appliquées selon trois catégories :

- **u** : propriétaire (user)  
- **g** : groupe (group)  
- **o** : autres utilisateurs (others)  
- **a** : tous (toutes les catégories)  

### 1.2 Commandes utiles

| Commande | Effet |
|----------|-------|
| `whoami` | affiche l’utilisateur courant |
| `id` | affiche UID, GID et groupes de l’utilisateur actuel |
| `groups` | liste les groupes auxquels appartient l’utilisateur |
| `ls -l fichier` | montre propriétaire et groupe d’un fichier |

Exemples :

```bash
whoami
id
groups
ls -l fichierTest1
```

---

## 2. Le compte `root`

### 2.1 Rôle de `root`

- `root` est le **super‑utilisateur** Linux, avec **tous les droits** sur le système.  
- Il peut lire, modifier ou supprimer n’importe quel fichier, même si les permissions standard ne le permettraient pas.  
- Sa gestion doit être **très prudente** (pas de `rm -rf /` ni de `chmod -R 777` système‑wide !).

### 2.2 Exemples pratiques

| Commande | Effet |
|----------|-------|
| `sudo whoami` | exécute `whoami` avec les droits `root` |
| `sudo -i` | ouvre un shell en tant que `root` (suivi de mot de passe) |
| `sudo -u user commande` | exécute `commande` avec l’identité de `user` |

---

## 3. Permissions de base (r, w, x)

### 3.1 Signification des symboles

| Symbole | Signification |
|---------|----------------|
| `r`     | lecture (read) |
| `w`     | écriture (write) |
| `x`     | exécution (execute) |

Exemples applicables à un fichier ou un répertoire :

- `r` = lire le contenu  
- `w` = modifier le contenu  
- `x` = exécuter un script ou entrer dans un répertoire  

### 3.2 Notation complète

Une chaîne de permissions a 10 caractères, par exemple :

```text
-rwxr-xr--
```

- 1er caractère : type de fichier (`-` = fichier, `d` = répertoire, `l` = lien symbolique).  
- Caractères 2–4 : `rwx` → propriétaire  
- Caractères 5–7 : `r-x` → groupe  
- Caractères 8–10 : `r--` → autres  

---

## 4. Permissions en notation symbolique

La notation **symbolique** utilise les lettres `r`, `w`, `x` et les opérateurs `+`, `-`, `=`.

### 4.1 Syntaxe générale

```bash
chmod [u/g/o/a][+/-/=][r/w/x] fichier
```

- `u` : propriétaire  
- `g` : groupe  
- `o` : autres  
- `a` : tous  

### 4.2 Exemples courants

| Commande | Effet |
|----------|-------|
| `chmod u=rwx fichier` | propriétaire = lecture + écriture + exécution |
| `chmod g=r,o=x fichier` | groupe = lecture seulement, autres = exécution seulement |
| `chmod u+x fichier` | ajoute le droit d’exécution au propriétaire |
| `chmod a-w fichier` | supprime l’écriture pour tous |
| `chmod go= fichier` | groupe et autres n’ont plus aucune permission |

Comparé à l’exercice :

```bash
chmod u=rwx,g=r,o=x fichierTest1
```

- `u=rwx` → propriétaire a tous les droits  
- `g=r` → groupe a lecture  
- `o=x` → autres ont exécution  

---

## 5. Permissions en notation octale

La notation **octale** utilise un chiffre pour chaque catégorie (`u`, `g`, `o`) :

- `r` = 4  
- `w` = 2  
- `x` = 1  

On additionne les valeurs pour chaque catégorie.

### 5.1 Tableau des valeurs

| Symbole | Valeur |
|---------|--------|
| `r`     | 4      |
| `w`     | 2      |
| `x`     | 1      |

Exemples :

- `rwx` → 4 + 2 + 1 = **7**  
- `r-x` → 4 + 0 + 1 = **5**  
- `r--` → 4 + 0 + 0 = **4**  

### 5.2 Exemples de modes courants

| Mode octal | Signification (symbole) | Usage typique |
|------------|--------------------------|---------------|
| `644`      | `rw-r--r--`              | fichier standard (propriétaire rw, groupe et autres r) |
| `640`      | `rw-r-----`              | fichier privé (propriétaire rw, groupe r, autres rien) |
| `755`      | `rwxr-xr-x`              | script ou dossier public (propriétaire tout, groupe et autres rx) |
| `700`      | `rwx------`              | fichier/dossier très privé (seul le propriétaire a accès) |
| `777`      | `rwxrwxrwx`              | **à éviter** sauf cas très spécifiques |

### 5.3 Exemple complet

```bash
chmod 644 fichier.txt
chmod 755 script.sh
chmod 700 dossierPrivé
```

---

## 6. Combinaison users / groupes / permissions

### 6.1 Modifier le propriétaire et le groupe

| Commande | Effet |
|----------|-------|
| `chown user fichier` | change le **propriétaire** uniquement |
| `chown user:group fichier` | change **propriétaire et groupe** |
| `chown -R user:group /dossier` | récursif (dossier et sous‑dossiers) |

Exemples :

```bash
chown marc fichier.txt
chown marc:devs script.sh
chown -R marc:devs /home/marc/projet
```

### 6.2 Lien entre `root`, permissions et sécurité

- Le `root` peut toujours modifier n’importe quel fichier, même si les permissions sont strictes.  
- Pour limiter les risques :
  - éviter de travailler en permanence avec `root`.  
  - utiliser `sudo` pour les commandes ponctuelles.  
  - respecter les bonnes pratiques de permissions (ex. pas de `777` inutile).  

---

## 7. Exercices de révision

Tu peux utiliser ces commandes pour vérifier ce que tu as appris :

```bash
ls -l fichierTest1
id
groups
stat -c "%a %A %n" fichierTest1   # permissions en octal + chaîne
```

---

> **Projet :** 420-FBD-LI – Soutien technique et formation  
> **Étudiant :** [Ton nom] – Cégep de Limoilou
