# jDeploy Configuration

Ce dossier contient la configuration jDeploy pour générer des installers natifs multi-plateformes de Presence IO.

## 🎯 Distribution Strategy

**GitHub Releases uniquement** - Aucun artefact n'est publié sur npm.

## 🔨 Build Local

### Option 1: Build Maven uniquement

```bash
# Depuis la racine du projet
./scripts/jdeploy-build.sh
```

Produit: `presence-desktop-app/target/presence-io-jdeploy.jar`

### Option 2: Build + Bundle jDeploy local

```bash
# Depuis la racine du projet
./scripts/jdeploy-bundle.sh
```

Produit: 
- JAR: `presence-desktop-app/target/presence-io-jdeploy.jar`
- Bundle: `jdeploy/jdeploy-bundle/`

### Option 3: Commandes manuelles

```bash
# Build Maven avec profil jDeploy
mvn -Pjdeploy -pl presence-desktop-app -am clean package -DskipTests

# Génération bundle jDeploy
cd jdeploy
npx jdeploy bundle
```

## 🚀 Publication (CI/CD)

La publication est automatisée via GitHub Actions:

**Workflow**: `.github/workflows/jdeploy-release.yml`  
**Déclencheur**: Publication d'une GitHub Release

### Process

1. Build Maven avec profil `-Pjdeploy`
2. Mise à jour de la version dans `package.json`
3. Publication via `npx jdeploy publish`
4. Installers créés pour la plateforme CI (Linux par défaut)

## 📦 Structure

```
jdeploy/
├── package.json          # Configuration jDeploy
├── .gitignore           # Exclusions build artifacts
├── README.md            # Ce fichier
└── jdeploy-bundle/      # (généré) Bundle jDeploy local
```

## ⚙️ Configuration

### package.json

- `jdeploy.jar`: Chemin vers le JAR stable produit par Maven
- `jdeploy.javaVersion`: Version Java requise (17)
- `jdeploy.javafx`: true (jDeploy télécharge JRE avec JavaFX)

### Maven Profile

Le profil `jdeploy` dans `presence-desktop-app/pom.xml`:
- Copie le JAR shaded existant (`*-app.jar`)
- Vers un nom stable (`presence-io-jdeploy.jar`)
- **Zéro impact** sur le build standard

## 🌐 Multi-Plateforme

### Phase 1 (Actuelle): Linux uniquement

Le workflow CI tourne sur `ubuntu-latest`.

⚠️ **Note JavaFX**: Le POM utilise `javafx.platform=mac-aarch64`. Pour build CI Linux:
- Option A: Utiliser macOS runner (`runs-on: macos-latest`)
- Option B: Refactoriser JavaFX par profils OS

### Phase 2 (Future): Matrix multi-OS

Pour générer des installers Windows/macOS natifs:
1. Ajouter matrix strategy au workflow
2. Adapter configuration jDeploy par OS
3. Voir `release.yml` existant comme référence

## 📚 Documentation

- [jDeploy Official Docs](https://www.jdeploy.com/docs/)
- [GitHub Action](https://github.com/shannah/jdeploy)
