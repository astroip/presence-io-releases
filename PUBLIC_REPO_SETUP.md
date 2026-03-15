# Configuration Dépôt Public de Releases pour jDeploy

## 🎯 Vue d'Ensemble

Ce guide explique comment configurer un dépôt GitHub **public** séparé pour distribuer les releases via jDeploy, tout en gardant votre code source **privé**.

---

## 📋 Étape 1 : Créer le Dépôt Public

### Sur GitHub

1. Aller sur https://github.com/new
2. **Repository name**: `presence-io-app-releases`
3. **Visibility**: ✅ **Public**
4. **Initialize with**: ✅ README
5. Créer le dépôt

### Contenu du README.md (exemple)

```markdown
# Presence IO

Application de gestion de présence automatique.

## 📥 Installation

**macOS, Windows, Linux**

[⬇️ Installer Presence IO](https://www.jdeploy.com/gh/astroip/presence-io-app-releases)

L'application inclut le JRE et se met à jour automatiquement.

## 📖 Documentation

- [Guide utilisateur](https://votre-site.com/docs)
- [FAQ](https://votre-site.com/faq)

## 🐛 Support

Pour toute question ou problème : support@votre-domaine.com

---

**Note**: Ce dépôt contient uniquement les releases publiques. Le code source est propriétaire.
```

---

## 🔑 Étape 2 : Créer un Personal Access Token (PAT)

### GitHub Settings

1. Aller sur https://github.com/settings/tokens
2. **Classic tokens** → **Generate new token (classic)**
3. **Note**: `jDeploy Public Releases`
4. **Expiration**: 90 days (ou No expiration pour simplifier)
5. **Scopes** à cocher:
   - ✅ `repo` (Full control of private repositories)
   - ✅ `write:packages`
   - ✅ `delete:packages`

6. Générer le token et **le copier** (vous ne pourrez plus le voir)

### Ajouter dans GitHub Secrets

1. Dans votre dépôt **privé** : `Settings` → `Secrets and variables` → `Actions`
2. **New repository secret**
   - Name: `PERSONAL_ACCESS_TOKEN`
   - Value: Coller le token copié
3. Sauvegarder

---

## ⚙️ Étape 3 : Configurer le Workflow

### Modifier le workflow

Éditez `.github/workflows/publish-prod-release.yml` :

```yaml
# Dépôt public dédié au canal stable
target_repository: astroip/presence-io-app-releases
```

---

## 🧪 Étape 4 : Tester

### Test en local (optionnel)

```bash
# 1. Build
./scripts/jdeploy-build.sh

# 2. Vérifier le JAR
ls -lh presence-desktop-app/target/presence-io-jdeploy.jar
```

### Test avec une Release

```bash
# 1. Créer un tag de test
git tag v1.0.0-test
git push origin v1.0.0-test

# 2. Créer une Release sur GitHub (dépôt privé)
# → Le workflow se déclenche automatiquement

# 3. Vérifier:
# - Dépôt public : nouvelle release créée
# - GitHub Actions : workflow réussi
```

---

## 🚀 Workflow de Release Complet

### Depuis Votre Dépôt Privé

```bash
# Option A: Script automatique
./scripts/release.sh 1.0.0

# Option B: Manuel
git tag v1.0.0
git push origin v1.0.0
# Puis créer la Release sur GitHub
```

### Ce Qui Se Passe Automatiquement

1. ✅ Workflow se déclenche dans dépôt **privé**
2. ✅ Build Maven + jDeploy
3. ✅ Publication jDeploy vers dépôt **public** dédié
4. ✅ Mise à jour du tag `jdeploy`
5. ✅ Création de la release publique versionnée
6. ✅ URL disponible : `https://www.jdeploy.com/gh/astroip/presence-io-app-releases`

### Vos Clients

1. Vont sur l'URL jDeploy
2. Cliquent "Install"
3. App installée **sans certificat Apple**
4. Auto-update automatique

---

## 📊 Résumé Architecture

```
┌──────────────────────────────────┐
│  astroip/presence-io (PRIVÉ)     │
│  - Code source                   │
│  - Développement                 │
│  - CI/CD                         │
└────────────┬─────────────────────┘
             │
             │ GitHub Actions
             │ (publish-prod-release.yml)
             ▼
┌──────────────────────────────────┐
│ astroip/presence-io-app-releases │
│ (PUBLIC)                         │
│  - README marketing              │
│  - GitHub Releases               │
│  - Binaires seulement            │
└────────────┬─────────────────────┘
             │
             │ jDeploy
             ▼
┌──────────────────────────────────┐
│  URL Installation Clients        │
│  jdeploy.com/gh/astroip/...      │
│  - Sans certificat Apple         │
│  - Auto-update                   │
└──────────────────────────────────┘
```

---

## ✅ Checklist de Configuration

- [ ] Dépôt public créé sur GitHub
- [ ] README marketing ajouté au dépôt public
- [ ] Personal Access Token créé
- [ ] Token ajouté aux Secrets GitHub (dépôt privé)
- [ ] Workflow modifié avec le bon nom de dépôt
- [ ] Test avec une release
- [ ] URL jDeploy testée
- [ ] Documentation client mise à jour

---

## 🔒 Sécurité

### Ce Qui Est Public
- ✅ Binaires (JAR compilés)
- ✅ README marketing
- ✅ Releases GitHub

### Ce Qui Reste Privé
- ✅ Code source Java
- ✅ Configuration Maven
- ✅ Secrets (clés de licence, etc.)
- ✅ Historique Git développement

---

## 🆘 Troubleshooting

### Erreur: "Resource not accessible by integration"

**Solution**: Vérifier que le `PERSONAL_ACCESS_TOKEN` a les permissions `repo`.

### Erreur: "Repository not found"

**Solution**: Vérifier `target_repository` dans `publish-prod-release.yml`.

### jDeploy ne trouve pas la release

**Solution**: Vérifier que la release versionnée et le tag `jdeploy`
sont bien créés sur le dépôt **public**.

---

## 📞 Support

Questions ? Créez une issue dans le dépôt privé.
