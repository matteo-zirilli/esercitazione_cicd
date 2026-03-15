```mermaid
graph LR
    %% Stili per le aree
    classDef locale fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    classDef cicd fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef prod fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef db fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef mlops fill:#e0f7fa,stroke:#006064,stroke-width:2px,stroke-dasharray: 5 5;

    %% 1. SVILUPPO LOCALE
    subgraph Locale [1. PC Sviluppatore / Codespace]
        Code[Codice Python <br> 'train.py']
        DF[Dockerfile API]
        DC_Dev[docker-compose.yml <br> 'Include MLflow']
        
        Code -.->|Testa| DF
        DF -.-> DC_Dev
    end

    %% IL CUORE MLOPS: MLFLOW
    subgraph MLOps [IL PONTE: MLflow]
        MLflow[(MLflow Server <br> Tracking & Registry)]
    end

    %% 2. GITHUB ACTIONS (CI/CD)
    subgraph CI_CD [2. GitHub Actions - Pipeline CI/CD]
        Push((Git Push <br> Solo Codice!))
        Download[Azione: Scarica Modello <br> da MLflow]
        Build[Build Immagine Docker <br> API + Modello]
        Registry[(Docker Hub <br> Magazzino)]
        
        Push --> Download
        Download --> Build
        Build -->|Carica| Registry
    end

    %% 3. SERVER DI PRODUZIONE
    subgraph Produzione [3. Server / Cloud]
        DC_Prod[docker-compose.prod.yml]
        ServerPull[Scarica nuova Immagine]
        RunApp[Container API <br> con Modello Integrato]
        
        DC_Prod --> ServerPull
        ServerPull --> RunApp
    end

    %% CONNESSIONI GLOBALI
    Code ==>|Salva Esperimenti e <br> Modelli Pesanti| MLflow
    Locale -->|Invia solo Codice| Push
    MLflow ==>|Fornisce il Modello <br> 'Produzione'| Download
    Registry -->|Rilascia Immagine Finale| ServerPull
    
    %% Applicazione Stili
    class Locale locale;
    class CI_CD cicd;
    class Produzione prod;
    class Registry,MLflow db;
    class MLOps mlops;
