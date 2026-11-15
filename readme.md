<!-- #!/usr/bin/env markdown
-*- coding: utf-8 -*-
region header
Copyright Torben Sickert (info["~at~"]tsickert.com) 16.12.2012

License
-------

This library written by Torben Sickert stands under a creative commons naming
4.0 international license.
See https://creativecommons.org/licenses/by/4.0/deed.de
endregion -->

<!--|deDE:Einsatzmöglichkeiten-->
<!--|frFR:Cas d'utilisation-->
Use case
--------

A composite GitHub action which checks out a node project and installs its
dependencies with the package manager pinned in its "package.json". It is the
first step of the lint, type check, build and test workflows of the projects
using it.
<!--deDE:
    Eine zusammengesetzte GitHub-Action, die ein Node-Projekt auscheckt und
    seine Abhängigkeiten mit dem in seiner "package.json" festgelegten
    Paketmanager installiert. Sie ist der erste Schritt der Lint-,
    Typprüfungs-, Build- und Test-Workflows der Projekte, die sie verwenden.
-->
<!--frFR:
    Une action GitHub composite qui récupère un projet node et installe ses
    dépendances avec le gestionnaire de paquets fixé dans son
    "package.json". Elle est la première étape des workflows de lint, de
    vérification des types, de construction et de test des projets qui
    l'utilisent.
-->

<div class="wd-table-of-contents">
    <h2 id="content">Content<!--deDE:Inhalt--><!--frFR:Contenu--></h2>
    <!--wd-table-of-contents-->
</div>

<!--|deDE:Verwendung-->
<!--|frFR:Utilisation-->
Usage
-----

The action checks out the repository itself, so the following steps can use
the installed dependencies right away:
<!--deDE:
    Die Action checkt das Repository selbst aus, die folgenden Schritte
    können die installierten Abhängigkeiten also direkt verwenden:
-->
<!--frFR:
    L'action récupère elle-même le dépôt, les étapes suivantes peuvent donc
    utiliser directement les dépendances installées:
-->

```yaml
name: Lint

on:
  push:
    branches:
      - main

jobs:
  main:
    runs-on: ubuntu-latest
    container: node:lts

    steps:
      - uses: thaibault/install-npm-package-dependencies-action@main

      - name: Run lint
        run: yarn lint
        shell: bash
```

The job needs "node" and "npm", e.g. via the "node:lts" container as above.
The action then runs the following steps:
<!--deDE:
    Der Job braucht "node" und "npm", z. B. über den Container "node:lts"
    wie oben. Die Action führt dann die folgenden Schritte aus:
-->
<!--frFR:
    Le job a besoin de "node" et "npm", par exemple via le conteneur
    "node:lts" comme ci-dessus. L'action exécute ensuite les étapes
    suivantes:
-->

1. The repository is checked out, by default only its latest commit.<!--deDE:Das Repository wird ausgecheckt, standardmäßig nur sein letzter Commit.--><!--frFR:Le dépôt est récupéré, par défaut uniquement son dernier commit.-->
2. "corepack" is installed if the Node.js version does not bundle it anymore (since Node.js 25) and enabled.<!--deDE:"corepack" wird installiert, falls die Node.js-Version es nicht mehr mitbringt (seit Node.js 25), und aktiviert.--><!--frFR:"corepack" est installé si la version de Node.js ne l'embarque plus (depuis Node.js 25), puis activé.-->
3. "corepack install" installs the package manager given by the "packageManager" field of "package.json", e.g. "yarn@4.18.1".<!--deDE:"corepack install" installiert den Paketmanager aus dem Feld "packageManager" der "package.json", z. B. "yarn@4.18.1".--><!--frFR:"corepack install" installe le gestionnaire de paquets indiqué par le champ "packageManager" du "package.json", par exemple "yarn@4.18.1".-->
4. "yarn install" installs all dependencies including the development ones.<!--deDE:"yarn install" installiert alle Abhängigkeiten einschließlich der Entwicklungsabhängigkeiten.--><!--frFR:"yarn install" installe toutes les dépendances, y compris celles de développement.-->

The "packageManager" field is required and has to name "yarn". The lockfile
may change during the installation, so a project without "yarn.lock" or with
an outdated one still installs.
<!--deDE:
    Das Feld "packageManager" ist erforderlich und muss "yarn" nennen. Die
    Lock-Datei darf sich während der Installation ändern, ein Projekt ohne
    "yarn.lock" oder mit einer veralteten installiert also trotzdem.
-->
<!--frFR:
    Le champ "packageManager" est obligatoire et doit désigner "yarn". Le
    fichier de verrouillage peut changer pendant l'installation, un projet
    sans "yarn.lock" ou avec un fichier obsolète s'installe donc quand même.
-->

<!--|deDE:Eingaben-->
<!--|frFR:Entrées-->
Inputs
------

- "debug": "true" sets "NODE_ENV" to "debug" during the installation, e.g. to get more output from install scripts. Default "false".<!--deDE:"debug": "true" setzt "NODE_ENV" während der Installation auf "debug", z. B. für mehr Ausgaben von Installationsskripten. Standard "false".--><!--frFR:"debug": "true" met "NODE_ENV" à "debug" pendant l'installation, par exemple pour obtenir plus de sorties des scripts d'installation. Par défaut "false".-->
- "fetch-depth": number of commits to check out, "0" checks out the whole history of all branches and tags. Default "1".<!--deDE:"fetch-depth": Anzahl der auszucheckenden Commits, "0" checkt die gesamte Historie aller Branches und Tags aus. Standard "1".--><!--frFR:"fetch-depth": nombre de commits à récupérer, "0" récupère tout l'historique de toutes les branches et de tous les tags. Par défaut "1".-->
- "fetch-tags": "true" also fetches the tags if "fetch-depth" is not "0". Default "false".<!--deDE:"fetch-tags": "true" holt zusätzlich die Tags, wenn "fetch-depth" nicht "0" ist. Standard "false".--><!--frFR:"fetch-tags": "true" récupère aussi les tags si "fetch-depth" n'est pas "0". Par défaut "false".-->

To pass the "debug" option of a manually started workflow through, declare it
as workflow input:
<!--deDE:
    Um die Option "debug" eines manuell gestarteten Workflows
    durchzureichen, deklarieren Sie sie als Workflow-Eingabe:
-->
<!--frFR:
    Pour transmettre l'option "debug" d'un workflow lancé manuellement,
    déclarez-la comme entrée du workflow:
-->

```yaml
on:
  workflow_dispatch:
    inputs:
      debug:
        description: Sets "NODE_ENV" to "debug".
        default: false
        type: boolean

jobs:
  main:
    runs-on: ubuntu-latest
    container: node:lts

    steps:
      - uses: thaibault/install-npm-package-dependencies-action@main
        with:
          debug: ${{ inputs.debug }}
```
