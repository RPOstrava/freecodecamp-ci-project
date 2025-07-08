# Git příkazy pro nahrání projektu na GitHub
## ✅ Step-by-step instructions

### 1. Project Initialization
```bash
cd /d/freecodecamp-gitlab-ci-main      # ujisti se, že jsi v adresáři projektu
git init                               # pokud jsi to ještě neudělal
git remote add origin https://github.com/RPOstrava/freecodecamp-ci-project.git  # nastav vzdálený repozitář
git add .                              # přidej všechny soubory do commitu
git commit -m "Přidání všech souborů projektu"   # vytvoř commit se zprávou
git branch -M main                     # přejmenuj větev na main (pokud ještě není)
git push -u origin main                # nahraj vše na GitHub

### 2. Build setup in `.gitlab-ci.yml`
```yaml
stages:                                          #Definice jednotlivých etap v CI
  - check
  - test

build website:                                   #Job pro sestavení aplikace
  image: node:16-alpine                          #Použitý Docker image
  stage: check
  script:
    - yarn install                               #Instalace závislostí
    - yarn build                                 #Sestavení projektu
  artifacts:
    paths:
      - build                                    #Výstupní složka, která se uchová mezi joby
```
```bash
$ git add .gitlab-ci.yml
$ git commit -m "Add build job with artifacts"   #Commit popisující přidání build kroku
$ git push
```

### 3. Check for build output
```yaml
check index.html:
  image: node:16-alpine                          #Stejný image kvůli kompatibilitě
  stage: check
  dependencies:
    - build website                              #Zaručí, že build běžel před tímto jobem
  script:
    - test -f build/index.html                   #Kontrola existence výstupního souboru
```
```bash
$ git add .gitlab-ci.yml
$ git commit -m "Add index.html check job"       #Commit pro kontrolu build výstupu
$ git push
```

### 4. Add unit testing
```yaml
unit tests:
  image: node:16-alpine
  stage: test
  script:
    - yarn install
    - yarn test                                  #Spuštění unit testů
```
```bash
$ git add .gitlab-ci.yml
$ git commit -m "Add unit test job"              #Commit se spuštěním testů
$ git push
```

### 5. Create `README.md`
```markdown
# GitLab CI/CD Project

This project demonstrates how to build, test, and validate a React application using GitLab CI/CD.

## Steps

1. Clone the repo
2. Run `yarn install`
3. Use `yarn build` to compile the app
4. `.gitlab-ci.yml` defines build, validation, and test jobs

## GitLab CI/CD Pipeline

- **build website** – Installs dependencies and builds React app.
- **check index.html** – Verifies output file exists.
- **unit tests** – Runs React unit tests using Jest.

## Artifacts
The `build/` folder is stored as an artifact so subsequent jobs can access compiled files.
```
```bash
$ git add README.md
$ git commit -m "Add project README"
$ git push