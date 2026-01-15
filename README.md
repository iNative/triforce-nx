# NgNest Init `v7.3`

Automazione Monorepo Nx per Angular & NestJS.


🛡️ End-to-End Type Safety

Il superpotere di questa architettura. Le interfacce TypeScript vivono in una libreria condivisa. Se modifichi un DTO nel Backend, il Frontend **si rompe in compilazione** prima ancora che tu possa committare.

⚡ Standardization

Configurazione automatica di **SCSS**, **Esbuild** e **Jest** per ogni nuovo componente generato.

🚀 Developer Experience

Struttura a cartelle scalabile, caching intelligente e naming convention forzate via script.

🚀 Quick Start: Inizializzazione
--------------------------------

Salva il seguente codice nel file `ngnest-init.sh`.  
Lo script gestisce interattivamente nomi, errori e configura i default del workspace.

ngnest-init.sh

Copia

    #!/bin/bash
    # CONFIGURAZIONE E COLORI
    VS_BLUE='\033[1;34m'
    VS_GREEN='\033[1;32m'
    VS_RED='\033[1;31m'
    VS_YELLOW='\033[1;33m'
    NC='\033[0m'
    
    # Gestione errori robusta
    set -e
    set -o pipefail
    
    # 1. RACCOLTA DATI (INPUT UTENTE)
    PROJECT_NAME=$1
    
    if [ -z "$PROJECT_NAME" ]; then
        echo -e -n "${VS_BLUE}➤ Inserisci il nome del Workspace (cartella root): ${NC}"
        read PROJECT_NAME
    fi
    
    if [ -z "$PROJECT_NAME" ]; then
        echo -e "${VS_RED}❌ Errore: Nome progetto mancante.${NC}"
        exit 1
    fi
    
    echo -e -n "${VS_BLUE}➤ Inserisci nome App Angular [default: frontend]: ${NC}"
    read FRONTEND_INPUT
    FRONTEND_APP_NAME=${FRONTEND_INPUT:-frontend}
    
    echo -e -n "${VS_BLUE}➤ Inserisci nome App NestJS [default: backend]: ${NC}"
    read BACKEND_INPUT
    BACKEND_APP_NAME=${BACKEND_INPUT:-backend}
    
    echo -e "\n${VS_YELLOW}------------------------------------------------${NC}"
    echo -e "🚀 RIEPILOGO: Workspace: $PROJECT_NAME | Front: $FRONTEND_APP_NAME | Back: $BACKEND_APP_NAME"
    echo -e "${VS_YELLOW}------------------------------------------------${NC}\n"
    
    # Check prerequisiti
    if ! command -v node &> /dev/null; then
        echo -e "${VS_RED}❌ Errore: Node.js non installato.${NC}"
        exit 1
    fi
    
    echo -e "${VS_BLUE}➤ Creazione Workspace Nx...${NC}"
    npx create-nx-workspace@latest "$PROJECT_NAME" --preset=apps --packageManager=npm --nxCloud=skip --yes
    cd "$PROJECT_NAME" || exit
    
    echo -e "\n${VS_BLUE}➤ Installazione Plugin...${NC}"
    npm install -D @nx/angular @nx/nest @nx/js
    
    echo -e "\n${VS_BLUE}➤ Configurazione Defaults (nx.json)...${NC}"
    # Inietta regole in nx.json per forzare SCSS e Jest su tutti i futuri componenti
    node -e "
    const fs = require('fs');
    const fileName = 'nx.json';
    try {
        const nxJson = JSON.parse(fs.readFileSync(fileName, 'utf8'));
        nxJson.generators = nxJson.generators || {};
        nxJson.generators['@nx/angular:component'] = { style: 'scss', inlineStyle: false };
        nxJson.generators['@nx/angular:library'] = { linter: 'eslint', unitTestRunner: 'jest' };
        fs.writeFileSync(fileName, JSON.stringify(nxJson, null, 2));
    } catch (e) { process.exit(1); }
    "
    
    echo -e "\n${VS_BLUE}➤ Generazione Frontend: $FRONTEND_APP_NAME...${NC}"
    npx nx g @nx/angular:application \
      --name="$FRONTEND_APP_NAME" \
      --directory="apps/$FRONTEND_APP_NAME" \
      --style=scss --routing --e2eTestRunner=cypress --bundler=esbuild --ssr=false --unitTestRunner=jest --no-interactive
    
    echo -e "\n${VS_BLUE}➤ Generazione Backend: $BACKEND_APP_NAME...${NC}"
    npx nx g @nx/nest:application \
      --name="$BACKEND_APP_NAME" \
      --directory="apps/$BACKEND_APP_NAME" \
      --frontendProject="$FRONTEND_APP_NAME" \
      --unitTestRunner=jest --no-interactive
    
    echo -e "\n${VS_BLUE}➤ Generazione Lib Shared...${NC}"
    npx nx g @nx/js:library --name=shared-data --directory=libs/shared-data --bundler=tsc --importPath="@$PROJECT_NAME/shared-data" --unitTestRunner=jest --no-interactive
    
    echo -e "\n${VS_GREEN}✅ SETUP COMPLETATO!${NC}"

Esegui il comando nel terminale:

Terminal

Copia

    bash ngnest-init.sh

🔄 Workflow: Shared Types
-------------------------

> Definisci il dato una volta sola, usalo ovunque.

### 1\. Definisci nella libreria condivisa

File: `libs/shared-data/src/lib/user.interface.ts`

user.interface.ts

Copia

    export interface UserProfile {
        id: string;
        email: string;
        role: 'admin' | 'user'; // Single Source of Truth
    }

### 2\. Importa (Automaticamente collegato)

Grazie al `tsconfig` centralizzato, importi la lib come se fosse un pacchetto npm.

app.component.ts

Copia

    import { UserProfile } from '@olon-platform/shared-data';

⚡ Nx Cheatsheet
---------------

Comandi validati per **Nx v20+**. Usa questi comandi per evitare errori di schema.

### 🏃‍♂️ Esecuzione

Terminal

Copia

    npx nx run-many -t serve -p frontend,backend

### ✨ Generatori

Tag

Comando

Descrizione

Frontend

`npx nx g @nx/angular:component features/login`

Genera componente (SCSS, HTML, TS) in `features/`.

Frontend

`npx nx g @nx/angular:service core/auth`

Genera servizio iniettabile in `src/app/core`.

Frontend

`npx nx g @nx/angular:guard core/guards/auth`

Genera una Route Guard funzionale.

Frontend

`npx nx g @nx/angular:interceptor core/jwt`

Genera un HttpInterceptor funzionale.

Backend

`npx nx g @nx/nest:resource products`

🚀 **Best Practice:** Crea Controller, Service, DTO e Module (CRUD).

Backend

`npx nx g @nx/nest:controller users`

Crea solo il Controller.

Backend

`npx nx g @nx/nest:service users`

Crea solo il Service (Logica di business).

Shared

`npx nx g @nx/js:lib shared-ui`

Crea una nuova libreria TS condivisa nel monorepo.

🐛 Troubleshooting
------------------

*   **Errore `$'\r': command not found`:** Il file ha terminatori Windows (CRLF). Esegui `dos2unix ngnest-init.sh` o converti in LF dal tuo editor.
*   **Errore `npm error Invalid URL`:** Causato anch'esso dai terminatori Windows. Converti in LF.
*   **Node Permissions:** Se ricevi `EACCES`, significa che stai usando Node di root. Usa **NVM** con il tuo utente standard.

© 2026 Guido Filippo Serio - Script Version 7.3
