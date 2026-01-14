# OloNx-AngularNestJS
Automazione Monorepo Nx per Angular &amp; NestJS. 
Olon Init v7.3
==============

Automazione Monorepo Nx per Angular & NestJS.

🛡️ End-to-End Type Safety

Il superpotere di questa architettura. Le interfacce TypeScript vivono in una libreria condivisa. Se modifichi un DTO nel Backend, il Frontend **si rompe in compilazione** prima ancora che tu possa committare.

⚡ Standardization

Configurazione automatica di **SCSS**, **Esbuild** e **Jest** per ogni nuovo componente generato.

🚀 Developer Experience

Struttura a cartelle scalabile, caching intelligente e naming convention forzate via script.

🚀 Quick Start: Inizializzazione
--------------------------------

Salva il seguente codice nel file `olon-init.sh`.  
Lo script gestisce interattivamente nomi, errori e configura i default del workspace.

olon-init.sh



Esegui il comando nel terminale:

Terminal

Copia

    bash olon-init.sh

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

*   **Errore `$'\r': command not found`:** Il file ha terminatori Windows (CRLF). Esegui `dos2unix olon-init.sh` o converti in LF dal tuo editor.
*   **Errore `npm error Invalid URL`:** Causato anch'esso dai terminatori Windows. Converti in LF.
*   **Node Permissions:** Se ricevi `EACCES`, significa che stai usando Node di root. Usa **NVM** con il tuo utente standard.

© 2026 Olon Platform Development Team - Script Version 7.3
