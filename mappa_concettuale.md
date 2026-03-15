```mermaid
graph LR
    %% Stili per le aree
    classDef locale fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    classDef cicd fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef prod fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef db fill:#fff3e0,stroke:#e65100,stroke-width:2px;

    %% 1. SVILUPPO LOCALE
    subgraph Locale [1. PC Sviluppatore / Codespace]
        Code[Codice Python / App]
        DF[Dockerfile <br> 'La Ricetta']
        DC_Dev[docker-compose.yml <br> 'Sviluppo Locale']
        
        Code -.-> DF
        DF -.-> DC_Dev
    end

    %% 2. GITHUB ACTIONS (CI/CD)
    subgraph CI_CD [2. GitHub Actions - Pipeline CI/CD]
        Push((Git Push))
        Test{Test Automatici <br> Tutto OK?}
        Build[Build Immagine Docker]
        Registry[(Docker Hub / Registry <br> 'Magazzino Immagini')]
        
        Push --> Test
        Test -->|Sì| Build
        Test -->|No| Stop[Blocca tutto e avvisa il dev]
        Build -->|Carica| Registry
    end

    %% 3. SERVER DI PRODUZIONE
    subgraph Produzione [3. Server di Produzione / Cloud]
        DC_Prod[docker-compose.prod.yml <br> 'File per Server']
        ServerPull[Il server scarica <br> la nuova immagine]
        RunApp[Container API / Web]
        RunDB[(Container Database)]
        
        DC_Prod --> ServerPull
        ServerPull --> RunApp
        RunApp <-->|Comunica| RunDB
    end

    %% CONNESSIONI GLOBALI
    Locale -->|Invia codice + YAML| Push
    Registry -->|Rilascia immagine| ServerPull
    
    %% Applicazione Stili
    class Locale locale;
    class CI_CD cicd;
    class Produzione prod;
    class Registry,RunDB db;
