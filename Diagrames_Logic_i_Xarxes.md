# Diagrames de Lògica i Arquitectura de Xarxa

Aquests diagrames se centren en la part més tècnica i d'enginyeria del projecte: com es desplega el sistema i com funcionen els algoritmes interns.

---

## 1. Diagrama de Desplegament (Deployment Diagram)

Mostra la distribució física dels components i els protocols de comunicació entre ells.

```mermaid
graph TD
    subgraph Client_Side [Estació de Treball / Operador]
        Browser[Navegador Web: Chrome/Firefox]
    end

    subgraph Server_Side [Dispositiu Lanaccess / NVR]
        WebSrv[Servidor Web: Angular App + Proxy]
        CoreApp[Core Service / Backend API]
        DB[(Base de Dades d'Alarmes)]
    end

    subgraph Field_Devices [Dispositius de Camp]
        Cam1[Càmera IP 1]
        Cam2[Càmera IP 2]
        Sensor[Sensors I/O]
    end

    %% Connexions
    Browser -- "HTTP / WebSocket (JSON)" --> WebSrv
    WebSrv -- "Local Process" --> CoreApp
    CoreApp -- "SQL Queries" --> DB
    
    %% Protocols de vídeo i hardware
    CoreApp -- "RTSP / HTTP" --> Cam1
    CoreApp -- "RTSP / HTTP" --> Cam2
    CoreApp -- "Digital Interface" --> Sensor
```

---

## 2. Diagrama d'Activitat: Lògica de Connexió al Vídeo

Explica el procés que segueix l'aplicació per establir una connexió de streaming i què fa si falla.

```mermaid
flowchart TD
    Start([Inici: Selecció de Càmera]) --> ReqConfig[Demanar configuració de la càmera]
    ReqConfig --> HasURL{Té URL de Stream?}
    
    HasURL -- No --> Error[Mostra error 'Càmera no configurada'] --> End([Fi])
    
    HasURL -- Sí --> InitPlayer[Inicialitzar reproductor WebRTC/HLS]
    InitPlayer --> Connect[Intentar connexió al Streaming Server]
    
    Connect --> Success{Connexió amb èxit?}
    
    Success -- Sí --> Play[Renderitzar frames de vídeo]
    Play --> Monitoring{Senyal actiu?}
    
    Monitoring -- Sí --> Play
    Monitoring -- No --> Retry[Intentar reconexió automàtica]
    
    Success -- No --> Retry
    
    Retry --> Limit{S'ha superat el límit?}
    Limit -- No --> Wait[Esperar 2 segons] --> Connect
    Limit -- Sí --> NoSignal[Mostra text 'NO SIGNAL'] --> End
```

---

## 3. Arquitectura de l'Aplicació (Mòduls Angular)

Aquest diagrama ajuda a entendre l'organització interna del codi que has desenvolupat.

```mermaid
graph LR
    AppModule --> AuthModule
    AppModule --> MainModule
    
    subgraph Features [Main Module]
        MainModule --> VideoModule
        MainModule --> ConfigModule
        MainModule --> MaintModule
    end
    
    VideoModule --> Cameras[Cameras List/Grid]
    VideoModule --> Playback[Recordings Player]
    VideoModule --> Alarms[Alarms Dashboard]
    
    ConfigModule --> System[System/Network Settings]
    ConfigModule --> IPVideo[IP Video Config]
    
    Shared[Shared Module] -.-> Features
    Core[Core Module] -.-> Features
```
