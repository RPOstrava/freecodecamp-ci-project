# Git příkazy pro nahrání projektu na GitHub
## ✅ Step-by-step instructions

### 1. Project Initialization
cd /d/freecodecamp-gitlab-ci-main      # ujisti se, že jsi v adresáři projektu
git init                               # pokud jsi to ještě neudělal
git remote add origin https://github.com/RPOstrava/freecodecamp-ci-project.git  # nastav vzdálený repozitář
git add .                              # přidej všechny soubory do commitu
git commit -m "Přidání všech souborů projektu"   # vytvoř commit se zprávou
git branch -M main                     # přejmenuj větev na main (pokud ještě není)
git push -u origin main                # nahraj vše na GitHub

### 2. Build setup in `.gitlab-ci.yml`
build website:
  image: node:16-alpine
  stage: check
  script:
    - yarn install
    - yarn build
  artifacts:
    paths:
      - build

# Příkazy pro commit a push změn
git add .gitlab-ci.yml
git commit -m "Add build job with artifacts"   # Commit popisující přidání build kroku
git push

### 3. Check for build output
check index.html:
  image: node:16-alpine
  stage: check
  dependencies:
    - build website
  script:
    - test -f build/index.html

# Příkazy pro commit a push změn
git add .gitlab-ci.yml
git commit -m "Add index.html check job"
git push

### 4. Add unit testing and dependent test jobs
stages:
  - check                # fáze kontroly (build, kontrola souborů)
  - test                 # fáze testování

build website:
  image: node:16-alpine  # použijeme Node.js 16 na Alpine Linuxu
  stage: check           # tento job je ve fázi check
  script:
    - yarn install       # nainstalujeme závislosti
    - yarn build         # spustíme build projektu (vytvoří složku build)
  artifacts:
    paths:
      - build            # zachováme složku build pro další joby

check index.html:
  image: node:16-alpine  # opět Node.js 16 Alpine (potřebujeme přístup k build složce)
  stage: check           # také ve fázi check
  script:
    - test -f build/index.html   # ověříme, zda existuje build/index.html
  needs:
    - build website       # tento job čeká na dokončení build website

run additional tests:
  image: node:16-alpine  # Node.js 16 Alpine pro testy
  stage: test            # fáze testování
  script:
    - yarn test          # spustíme testy definované v package.json
  needs:
    - build website      # závislost na build website
    - check index.html   # závislost na kontrole index.html

run final tests:
  image: node:16-alpine  # Node.js 16 Alpine i zde
  stage: test            # stále ve fázi testování
  script:
    - yarn test:final    # spustíme finální testy, které si definujeme
  needs:
    - run additional tests  # tento job závisí na úspěšném dokončení run additional tests

# Příkazy pro commit a push změn
git add .gitlab-ci.yml
git commit -m "Add unit and dependent test jobs"
git push

---

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
- **run additional tests** – Runs unit tests after build and index.html check.  
- **run final tests** – Runs final test suite after additional tests pass.  

## Artifacts

The `build/` folder is stored as an artifact so subsequent jobs can access compiled files.

# Příkazy pro commit a push README.md
git add README.md
git commit -m "Add project README"
git push
