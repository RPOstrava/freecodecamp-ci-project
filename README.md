
Git příkazy pro nahrání projektu na GitHub


Step-by-step instructions

# 1. Project Initialization
cd /d/freecodecamp-gitlab-ci-main      # ujisti se, že jsi v adresáři projektu
git init                               # pokud jsi to ještě neudělal
git remote add origin https://github.com/RPOstrava/freecodecamp-ci-project.git  # nastav vzdálený repozitář
git add .                              # přidej všechny soubory do commitu
git commit -m "Přidání všech souborů projektu"   # vytvoř commit se zprávou
git branch -M main                     # přejmenuj větev na main (pokud ještě není)
git push -u origin main                # nahraj vše na GitHub

# 2. Build setup in `.gitlab-ci.yml`
build website:
  image: node:16-alpine       # použijeme Node.js 16 na Alpine Linuxu - lehký image pro běh jobu
  stage: check                # job bude ve fázi 'check', kde se kontroluje a staví aplikace
  script:
    - yarn install            # nainstalujeme závislosti z package.json
    - yarn build              # spustíme build, vytvoří složku 'build' s produkční verzí
  artifacts:
    paths:
      - build                 # uložíme složku 'build' jako artifact, aby ji mohly další joby použít

# Po úpravě souboru .gitlab-ci.yml spusť tyto příkazy, abys změny commitoval a pushnul:
git add .gitlab-ci.yml
git commit -m "Add build job with artifacts"   # commit s popisem změny - přidání build jobu
git push                                      # pushni na vzdálený repozitář

# 3. Check for build output - kontrola, jestli build vytvořil index.html
check index.html:
  image: node:16-alpine                          # používáme stejný Node.js 16 Alpine image
  stage: check                                  # také ve fázi 'check'
  dependencies:
    - build website                             # tento job bude čekat na dokončení 'build website' jobu
  script:
    - test -f build/index.html                  # ověří, že soubor 'build/index.html' existuje

# Po přidání check index.html jobu commitni a pushni:
git add .gitlab-ci.yml
git commit -m "Add index.html check job"       # commit pro kontrolu existence build výstupu
git push

# 4. Unit testing a další testy
unit tests:
  image: node:16-alpine              # Node.js 16 Alpine image pro testování
  stage: test                       # fáze testování
  script:
    - yarn install                  # nainstaluj závislosti (pro testy)
    - yarn test                    # spusť unit testy definované v package.json

check index.html:
  image: node:16-alpine              # Node.js 16 Alpine pro kontrolu souboru
  stage: check
  script:
    - test -f build/index.html      # ověří existenci build/index.html
  needs:
    - build website                 # čeká na dokončení jobu build website

run additional tests:
  image: node:16-alpine              # Node.js 16 Alpine pro další testy
  stage: test
  script:
    - yarn test                    # spusť další testy z package.json
  needs:
    - build website                # závislost na dokončení build
    - check index.html             # závislost na úspěšné kontrole index.html

run final tests:
  image: node:16-alpine              # Node.js 16 Alpine pro finální testy
  stage: test
  script:
    - yarn test:final              # spusť finální testy, které si definuješ
  needs:
    - run additional tests         # čeká na úspěšné dokončení run additional tests

------------------------------------------------------------------
5. Jak pipeline funguje
------------------------------------------------------------------

- build website: Instaluje závislosti a sestavuje React aplikaci.
- check index.html: Kontroluje, zda vznikl soubor build/index.html.
- unit tests: Spouští unit testy pomocí Jest.
- Další testy jsou závislé na úspěšném dokončení buildu a kontroly.
- Artefakty (build složka) jsou uchovány a zpřístupněny pro následující joby.


------------------------------------------------------------------
6. Jak spustit jednotlivé kroky ručně (v terminálu)
------------------------------------------------------------------

# Přidání .gitlab-ci.yml do repozitáře a push s popisem
git add .gitlab-ci.yml
git commit -m "Add CI/CD jobs"
git push

# Přidání README.md a push
git add README.md
git commit -m "Add project README"
git push


==================================================================
This helps me understand the pipeline creation process in Gitlab.
==================================================================
