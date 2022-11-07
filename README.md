
# CONFIGURAR PROYECTO

## CONFIGURACIÓN INICIAL

### INICIAR PROYECTO
    npm init -y
    
### INICIAR GIT
    git init
    
### AÑADIR SINGLEQUOTES
    "prettier": {
      "singleQuote": true
    }
    
### AÑADIR GITIGNORE ( NODE MODULOS)
    Ver > paleta de comandos > Add gitignore node
    
### AÑADIR EDITOR CONFIG
	- Necesitamos plugin EditorConfig for VS Code
	  Botón derecho añadir edit.config
    
### Crea el fichero README. MD

### INITIAL COMMIT
    git add .
    git commit -m "Initial Commit"
    
### Crea un repositorio en GITHUB

### PUSHEAR EL PROYECTO
    git remote add origin https://github.com/nombre-repositorio
    
    git branch -M main
    
    git push -u origin main
    
### PROTEGER MAIN BRANCH
	- Settings> Branches>Add branch protection rules
	- Require a pull request before merging
	- A partir de aquí siempre trabajaremos en una rama distinta a main

### INSTALAR HUSKY
    npm install husky -D  
    
    npm set-script prepare "husky install"        
    
    npm run prepare
    
    		        
    
### AÑADIR HOOK PRE-PUSH y COMMIT-MSG
#### PRE-PUSH
    #!/bin/sh
    
    . "$(dirname "$0")/_/husky.sh"
    
    				  
    
    local_branch_name="$(git rev-parse --abbrev-ref HEAD)"
    
    				  
    
    valid_branch_regex='^((hotfix|bugfix|feature)\/[a-zA-Z0-9\-]+)$'
    
    				  
    
    message="Please check your branch name."
    
    				  
    
    if [[ ! $local_branch_name =~ $valid_branch_regex ]]; then
    
        echo -e "\033[0;31m$message"
    
        exit 1
    
    fi
    
    				 
    exit 0
    
#### COMMIT-MSG
    #!/bin/sh
    
    . "$(dirname "$0")/_/husky.sh"
    
    				  
    
    while read line; do
    
        # Skip comments
    
        if [ "${line:0:1}" == "#" ]; then
    
            continue
    
        fi
    
        if [ ${#line} -ge 72 ] || [ ${#line} -le 10 ]; then
    
            echo -e "\033[0;31mThe length of the message has to be between 10 and 72 characters."
    
            exit 1
    
        fi
    
    done < "${1}"
    
    				  
    
    exit 0
    
## CONFGURAR ESLINT
### INSTALAR
    npm i -D eslint
    
### CONFIGURAR
    npx eslint --init
    
Esto lanza un configurador
### OPCIONES
	To check syntax, find problems, and enforce code style
	JavaScript modules (import/export)
	FRAMEWORK
	TYPESCRIPT
	ENTORNO: NODE Y BROWSER SELECCIONAR AMBAS CON BARRA ESPACIADORA
	Seleccionar la configuración
	FORMATO DE CONFIGURACIÓN :JSON
	
### CONFIGURAR PARA QUE NO CREE CONFLICTO CON PRETTIER
Repo: https://github.com/prettier/eslint-config-prettier
### INSTALAR
    npm i -D eslint-config-prettier
    
### EXTENDER LA DEPENDECIA EN .eslintrc.json
#### Después de la configuración elegida
    "extends": ["standard",  "prettier"]
    
       "overrides": [
    
       ],
    
## CONFIGURAR JEST
### INSTALL
    npm install --save-dev jest
    
### AÑADIR CONFIGURACION PARA QUE FUNCIONE CON JAVASCRIPT Y VSCODE
Deberemos hacer algunas modificaciones para que no cree conflicto

#### AÑADIR EN PACKAGE.JSON

	    "type": "module",
    
    "devDependencies": {
    
          "@babel/plugin-transform-modules-commonjs": "^7.19.6",
    
      },
    
    "babel": {
    
         "env": {
    
             "test": {
    
                 "plugins": [
    
                     "@babel/plugin-transform-modules-commonjs"
    
                 ]
    
             }
    
         }
    
     }
    
### ESLINTCR.JSON
    "env": {
    
           "jest":true
    
       },
    
### CREAR FICHERO jsconfig.json
	{

	"typeAcquisition": {

		"include": ["jest"]
		}

	}
### OPCIONAL AÑADIR TIPOS JEST PARA QUE SALGA OPCIONES EN VSCODE PACKAGE.JSON
    "devDependencies": {
          "@types/jest": "^29.2.1",
      },
    
#### AÑADIR SCRIPT EN PACKAGE.JSON PARA COVERAGE
    "test":"jest --watchAll --coverage",
    
#### NPM RUN PARA INSTALAR LO AÑADIDO

## CONFIGURAR GITHUB ACTIONS
### Crear workflow en github actions
	Crear fichero audit.yml
#### FICHERO AUDIT
    name: Audit
    on: push
    
    jobs:
    
      audit:
    
        runs-on: ubuntu-latest
    
        name: Audit
    
    				 
        steps:
    
          - name: Git checkout
    
            uses: actions/checkout@v2
    
          - name: Check if .editorconfig exists
    
            uses: andstor/file-existence-action@v1
    
            with:
    
              files: '.editorconfig'
    
              allow_failure: true
    
    				  
    
          - name: EditorConfig validation
    
            uses: snow-actions/eclint@v1.0.1
   
    				  
    
          - name: Ensure node_modules is ignored by Git
    
            uses: dkershner6/gitignore-parser@v1
    
            with:
    
              must_deny: 'node_modules/'
    
    				  
    
          - name: Install modules
    
            run: npm ci
    
    				  
    
          - name: ESLint validation
    
            run: npx eslint --ignore-path .gitignore .
    
    				  
    
          - name: Check commit message length
    
            uses: gsactions/commit-message-checker@v1
    
            with:
    
              pattern: '^[^#].{10,74}'
    
              error: 'The commit message length must be between 10 and 72'
    
              excludeDescription: 'true' # optional: this excludes the description body of a pull request
    
              excludeTitle: 'true' # optional: this excludes the title of a pull request
    
              checkAllCommitMessages: 'true' # optional: this checks all commits associated with a pull request
    
### CHECKEAR EN LOCAL LOS INDENTADOS
    npx eclint check
    
### CAMBIAR FICHERO EDITOR CONFIG
#### Nueva configuración para añadir linea al final e indent size para yml y así evitar errores con la configuración  GitHubActions audits
    # EditorConfig is awesome: https://EditorConfig.org
    
    				  
    
    # top-most EditorConfig file
    
    root = true
    		  
    
    [*]
    
    indent_style = space
    
    indent_size = 4
    
    end_of_line = crlf
    
    charset = utf-8
    
    trim_trailing_whitespace = false
    
    insert_final_newline = true;
    
    				  
    
    [*.yml]
    
    indent_size = 2
    
### CONFIGURAR SONAR
https://sonarcloud.io/
#### PASOS
	Después de añadir nuestro proyecto
	Ir Administration Analysis Method > GitHub Action Follow tutorial
	In your GitHub repository, go to Settings > Secrets > Actions  and create a new secret with the following
	Hay que crear un nuevo repositorio Secret
	  In the Name field, enter SONAR_TOKEN  
    In the Value field, enter ***num***
    
### Continuar el tutorial
#### Create or update a  `.github/workflows/build.yml`  file
	En nuestro caso seleccionamos OTHER (JS, TS...)
	Crear un fichero .yml para incorporar a Github Action
	Podemos hacerlo en local o en GITHUB ACTIONS
#### CREAR FICHERO SONAR.yml
### CREAR fichero sonar-project.properties
#### Desactivar análisis automático
### ACTUALIZAR SONAR.YML ACTION
	Le añadimos que corra los test
Tenemos que añadir un nuevo script test, para que no se queden en modo watch
    "scripts": {
    
           "test:prod": "jest --watchAll --coverage --watchAll=false",
    
       },
    
### HACER QUE SONAR EXCLUYA LOS TEST
Cambiar fichero sonar-project.properties para excluir lo que no queremos testear
    sonar.javascript.lcov.reportPaths=coverage/lcov.info
    
    sonar.coverage.exclusions= index.js, modules/**/*.test.*
    
