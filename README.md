==================================================================
Git příkazy pro nahrání projektu na GitHub
==================================================================

Step-by-step instructions

------------------------------------------------------------------
1. Project Initialization
------------------------------------------------------------------

cd /d/freecodecamp-gitlab-ci-main      # ujisti se, že jsi v adresáři projektu
git init                               # pokud jsi to ještě neudělal
git remote add origin https://github.com/RPOstrava/freecodecamp-ci-project.git  # nastav vzdálený repozitář
git add .                              # přidej všechny soubory do commitu
git commit -m "Přidání všech souborů projektu"   # vytvoř commit se zprávou
git branch -M main                     # přejmenuj větev na main (pokud ještě není)
git push -u origin main                # nahraj vše na GitHub


------------------------------------------------------------------
2. Build setup in .gitlab-ci.yml
------------------------------------------------------------------

build website:
  image: node:16-alpine                # použijeme Node.js 16 Alpine Linux
  stage: check                        # fáze build
  script:
    - yarn install                   # instalace závislostí
    - yarn build                    # sestavení projektu
  artifacts:
    paths:
      - build                       # zachování build složky jako artefaktu


------------------------------------------------------------------
3. Check for build output
------------------------------------------------------------------

check index.html:
  image: node:16-alpine               # Node.js 16 Alpine pro přístup k build složce
  stage: check                      # také ve fázi check
  dependencies:
    - build website                 # závislost na build website jobu
  script:
    - test -f build/index.html      # kontrola, zda soubor existuje


------------------------------------------------------------------
4. Unit testing a další testy
------------------------------------------------------------------

unit tests:
  image: node:16-alpine              # Node.js 16 Alpine pro testy
  stage: test                       # fáze testování
  script:
    - yarn install                  # nainstaluj závislosti
    - yarn test                    # spusť unit testy

check index.html:
  image: node:16-alpine              # Node.js 16 Alpine pro kontrolu souboru
  stage: check
  script:
    - test -f build/index.html      # ověř existence build/index.html
  needs:
    - build website                 # musí být hotový build website

run additional tests:
  image: node:16-alpine              # Node.js 16 Alpine pro další testy
  stage: test
  script:
    - yarn test                    # spusť další testy z package.json
  needs:
    - build website
    - check index.html

run final tests:
  image: node:16-alpine              # Node.js 16 Alpine pro finální testy
  stage: test
  script:
    - yarn test:final              # spusť finální testy, které si definuješ
  needs:
    - run additional tests          # čeká na úspěšné dokončení run additional tests


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
