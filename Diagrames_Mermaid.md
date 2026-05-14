# Diagrames en Mermaid (UML) per al TFG

Aquest fitxer conté el codi **Mermaid** per a tots els diagrames proposats a la guia. Pots visualitzar aquests diagrames directament a GitHub, VS Code (amb l'extensió de Mermaid) o copiant el codi a l'editor online: [Mermaid Live Editor](https://mermaid.live/).

---

## 1. Diagrama de Casos d'Ús

```mermaid
flowchart TD
    %% Actors
    Op["👤 Operador"]
    Admin["🔐 Administrador"]
    Sys["🖥️ Sistema Extern (NVR/Càmera)"]

    subgraph WebCoreView [Aplicació WebCoreView]
        UC1(["Login / Logout"])
        UC2(["Veure Vídeo en Directe"])
        UC3(["Cercar i Reproduir Gravacions"])
        UC4(["Gestionar Alarmes"])
        UC5(["Configurar Xarxa i Sistema"])
        UC6(["Gestionar Usuaris i Permisos"])
        UC7(["Consultar Logs i Reports"])
        UC8(["Control PTZ"])
    end

    %% Relacions Operador
    Op --- UC1
    Op --- UC2
    Op --- UC3
    Op --- UC4
    Op --- UC8

    %% Relacions Administrador
    Admin --- UC1
    Admin --- UC5
    Admin --- UC6
    Admin --- UC7
    Admin --- UC2

    %% Relacions Sistema
    UC2 --- Sys
    UC3 --- Sys
    UC5 --- Sys
```

---

## 2. Diagrama de Classes

```mermaid
classDiagram
    class Camera {
        +String id
        +String name
        +String type
        +String logicId
        +String systemId
        +IpConfig ipConfig
        +Preset[] presets
        +Codec[] codecList
    }

    class Codec {
        +String rtspUri
        +int width
        +int height
        +int fps
        +int kbps
        +String profileToken
    }

    class Preset {
        +String name
        +String token
        +String position
    }

    class Alarm {
        +String alarm_id
        +String db_id
        +String description
        +DateTime timestamp
        +String priority
        +String status
        +boolean is_read
        +boolean is_archived
    }

    class User {
        +String id
        +String name
        +String profile
        +String grants
    }

    class IoItem {
        +String id
        +String name
        +String topic
        +String data
        +String[] capabilities
    }

    Camera *-- Codec : conté
    Camera *-- Preset : conté
    Alarm --> Camera : associada a
    User ..> Camera : té permisos sobre
```

---

## 3. Diagrames de Seqüència

### A. Flux de Login
```mermaid
sequenceDiagram
    participant U as Usuari
    participant UI as LoginComponent
    participant S as AuthService
    participant B as Backend (API)

    U->>UI: Introdueix credencials
    UI->>S: login(user, password)
    S->>B: POST /login
    B-->>S: 200 OK (Token/Success)
    S->>UI: Sessió iniciada
    UI->>U: Redirecció a Home
```

### B. Visualització de Vídeo
```mermaid
sequenceDiagram
    participant U as Usuari
    participant UI as CamerasComponent
    participant S as VideoService
    participant ST as Streaming Server

    U->>UI: Selecciona Càmera
    UI->>S: getStreamUrl(cameraId)
    S->>ST: Request RTSP/Websocket
    ST-->>S: Stream Data
    S-->>UI: Render Video Frame
    UI->>U: Mostra vídeo en pantalla
```

### C. Canvi de Configuració (amb Lock)
```mermaid
sequenceDiagram
    participant A as Admin
    participant UI as ConfigComponent
    participant S as ConfigService
    participant B as Backend (API)

    A->>UI: Entra a pàgina de config
    UI->>S: acquireLock(path)
    S->>B: GET /lock
    B-->>S: Lock Token (Success)
    S-->>UI: Mode Edició Activat
    A->>UI: Modifica dades i prem Guardar
    UI->>S: saveConfig(data, token)
    S->>B: POST /config (with token)
    B-->>S: 200 OK
    S-->>UI: Cambis aplicats
    UI->>A: Mostra missatge d'èxit
```

---

## 4. Diagrama d'Estats (Alarma)

```mermaid
stateDiagram-v2
    [*] --> New : Esdeveniment detectat
    New --> Read : Usuari obre l'alarma
    Read --> Acknowledged : Usuari confirma recepció
    Acknowledged --> Archived : Usuari arxiva
    Read --> Archived : Usuari arxiva directament
    Archived --> Deleted : Usuari esborra
    Deleted --> [*]
```

---

## 5. Diagrama de Components (Arquitectura)

```mermaid
graph TD
    subgraph Client_Browser [Navegador - Angular App]
        UI[Shared Components]
        Video[Video Module]
        Config[Config Module]
        Maint[Maintenance Module]
        Core[Core Services/Guards]
    end

    subgraph Backend_Server [Backend / NVR]
        API[REST API]
        WS[WebSocket Server]
        Stream[Streaming Service]
    end

    Core --> API
    Core --> WS
    Video --> Stream
    Video --> WS
    Config --> API
    Maint --> API
```
