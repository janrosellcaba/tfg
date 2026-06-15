# Planificació i Stack Tecnològic (Mermaid)

Aquest fitxer conté diagrames visuals per complementar la memòria del TFG en l'apartat de gestió del projecte i arquitectura tecnològica.

---

## 1. Cronograma del Projecte (Diagrama de Gantt)

Aquest diagrama mostra les fases de desenvolupament des de l'inici fins a la presentació final. Els terminis són aproximats basats en el progrés actual del projecte.

```mermaid
gantt
    title Planificació del TFG - WebCoreView
    dateFormat  YYYY-MM-DD
    section Fase d'Anàlisi
    Estudi de requeriments i UX       :active, des1, 2026-02-01, 20d
    Disseny de l'arquitectura Angular : des2, after des1, 15d
    
    section Desenvolupament Core
    Implementació Auth i Guards       : des3, after des2, 10d
    Mòdul de Vídeo i Streaming (WebRTC/RTSP) : des4, after des3, 30d
    Gestió d'Alarmes i WebSockets     : des5, after des4, 20d
    
    section Configuració i Sistema
    Mòdul de Configuració de Xarxa     : des6, 2026-04-01, 20d
    Sistema de Locking i Validació    : des7, after des6, 15d
    Manteniment i Reports             : des8, after des7, 15d
    
    section Finalització
    Fase de Proves i Bug Fixing       : crit, des9, 2026-05-10, 15d
    Redacció de la Memòria (FIB)      : active, des10, 2026-05-01, 30d
    Preparació de la Presentació      : des11, after des10, 10d
```

---

## 2. Mapa Mental de Tecnologies (Stack Tecnològic)

Un resum visual de totes les tecnologies i conceptes aplicats en el projecte.

```mermaid
mindmap
  root((WebCoreView))
    Frontend
      Angular 19
      TypeScript
      Signals / State Management
      Reactive Forms
    Estils
      Vanilla CSS / SCSS
      Open Sans Typography
      Responsive Design
    Comunicació
      HTTP / REST API
      WebSockets (Real-time)
      RTSP / Streaming
      HLS
    Eines i Processos
      Git / GitHub
      NPM
      Mermaid (Docs)
      Angular CLI
    Conceptes d'Enginyeria
      Concurrency (Locking)
      RBAC (Grants/Permissions)
      System Maintenance
      Network Security
```

---

## 3. Flux d'Interacció de l'Usuari (User Journey)

Aquest diagrama ajuda a explicar com un usuari utilitza l'app per resoldre un problema (per exemple, revisar una alarma).

```mermaid
journey
    title Revisió d'una Incidència de Seguretat
    section Accés
      Login al sistema: 5: Usuari
      Visualització de l'estat general: 4: Usuari, Sistema
    section Detecció
      Recepció de notificació d'alarma: 5: Sistema
      Identificació de la càmera afectada: 4: Usuari
    section Resolució
      Obertura del vídeo en directe: 5: Usuari, Sistema
      Control PTZ per enfocar l'objecte: 3: Usuari
      Consulta de gravacions anteriors: 4: Usuari, Sistema
    section Finalització
      Arxivar l'alarma: 5: Usuari
      Generar report d'estat: 4: Usuari
```
