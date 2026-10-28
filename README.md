# TP 2 Devops : Intégration de Git avec Jenkins — Guide pas-à-pas
**Oussema Khedira — Groupe 4 — ISG Sousse — 2026-2027 (Mme Zaineb Sakhrawi)**

Ce dossier contient le projet complet du TP2 :
- `index.html` : page web (code du TP)
- `style.css` : styles (code du TP)
- `build_steps_a_coller_dans_jenkins.txt` : commandes batch pour l'étape de build Jenkins
- `Jenkinsfile` : pipeline déclaratif (partie bonus du TP)
- `README.md` : ce guide

---

## Étape 1 — Préparer le dépôt Git

Ouvrir l'invite de commandes (cmd) dans le dossier du projet, puis :

```
cd C:\DevOps\mon-projet
git init
git add .
git commit -m "Premier commit : application web TP2 Jenkins"
git branch -M main
```

**Option A — GitHub (recommandée) :**
1. Créer un dépôt vide sur https://github.com/new (nom : `mon-projet`, public).
2. Le lier et pousser :
```
git remote add origin https://github.com/<ton-nom>/mon-projet.git
git push -u origin main
```
(If GitHub demande un mot de passe : utiliser un Personal Access Token, pas le mot de passe du compte.)

**Option B — local seulement :** rester avec le dossier `C:\DevOps\mon-projet`, Jenkins ira le lire directement.

---

## Étape 2 — Créer le job Jenkins

1. Ouvrir Jenkins : http://localhost:8080
2. **New Item**
3. Nom : `TP_Jenkins_Git`
4. Type : **Freestyle project** → OK
5. Description (optionnel, cf. annexe du TP) : *Automatisation des builds pour le projet Jenkins*
6. **Discard old builds** : cocher (économie d'espace)
7. **GitHub project** : coller l'URL du projet `https://github.com/<ton-nom>/mon-projet`

## Étape 3 — Source Code Management

1. Choisir **Git**
2. Repository URL :
   - GitHub : `https://github.com/<ton-nom>/mon-projet.git`
   - Local : `C:\DevOps\mon-projet`
3. Branch : `*/main`
4. Si GitHub privé → Credentials : ajouter ton identifiant/token.

## Étape 4 — Déclenchement automatique (Build Triggers)

1. Cocher **Poll SCM**
2. Saisir : `H/5 * * * *`
   → Jenkins vérifie toutes les 5 minutes si le dépôt a changé.

*(Alternative vue en annexe : GitHub hook trigger for GITScm polling, si un webhook est configuré.)*

## Étape 5 — Étapes de build (Build Steps)

1. **Add build step** → **Execute Windows batch command**
2. Coller le contenu de `build_steps_a_coller_dans_jenkins.txt` :
```
echo === Étape 1 : Vérification du dépôt ===
git status
echo === Étape 2 : Afficher le contenu du projet ===
dir
echo === Étape 3 : Simuler un déploiement local ===
echo Le fichier index.html est prêt à être affiché
echo === Étape 4 : Fin du build ===
echo SUCCESS
```
3. **Save**

## Étape 6 — Tester

1. Cliquer **Build Now**
2. Ouvrir **Console Output** → vérifier les 4 étapes et `SUCCESS` à la fin.
3. Tester le déclenchement auto : modifier `index.html` (ex. changer le texte du `<h1>`), puis :
```
git add .
git commit -m "Modification pour tester le poll SCM"
git push
```
→ dans les 5 minutes, Jenkins relance le build tout seul.

---

## Bonus — Pipeline avec Jenkinsfile

1. Le fichier `Jenkinsfile` est déjà à la racine du projet (pipeline du TP, avec l'URL du dépôt à adapter : remplace `OussemaKhedira` par ton nom GitHub si différent).
2. Le pousser dans le dépôt :
```
git add Jenkinsfile
git commit -m "Ajout Jenkinsfile (pipeline)"
git push
```
3. Dans Jenkins : **New Item** → nom `TP_Pipeline_Jenkins` → type **Pipeline** :
   - Definition : **Pipeline script from SCM**
   - SCM : **Git** → URL du dépôt
   - Branch : `*/main`
   - Script Path : `Jenkinsfile`
   - Save → **Build Now** → regarder la **Console Output**.

---

## Récap des points demandés par le TP (checklist)

- [ ] Fichiers `index.html` et `style.css` créés
- [ ] Dépôt Git prêt (GitHub ou local)
- [ ] Job freestyle `TP_Jenkins_Git` connecté au dépôt
- [ ] Poll SCM `H/5 * * * *`
- [ ] Build step batch (4 étapes, fin `SUCCESS`)
- [ ] Build Now + Console Output vérifiés
- [ ] Bonus : Jenkinsfile + job Pipeline
