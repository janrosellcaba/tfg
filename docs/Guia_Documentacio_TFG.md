# Guia de Documentació TFG: WebCoreView

Aquest document serveix com a guia detallada per a la creació dels diagrames UML, històries d'usuari i altres elements de documentació per al projecte **WebCoreView**. L'objectiu és que qualsevol persona pugui generar aquests continguts sense tenir un coneixement previ profund del codi, basant-se en les estructures i funcionalitats aquí descrites.

---

## 1. Històries d'Usuari (User Stories)

Les històries d'usuari han de seguir el format:
> **Com a** [rol], **vull** [acció] **per tal de** [benefici].

### Llistat de funcionalitats clau per a redactar:
- **Monitoratge de Vídeo**:
    - Com a operador, vull veure el vídeo en directe de les càmeres per supervisar la seguretat en temps real.
    - Com a operador, vull controlar el PTZ (moviment/zoom) de les càmeres per seguir objectes d'interès.
- **Gestió d'Alarmes**:
    - Com a supervisor, vull rebre notificacions d'alarmes en temps real per actuar ràpidament davant d'incidències.
    - Com a administrador, vull configurar regles d'alarma per automatitzar la detecció d'esdeveniments.
- **Configuració del Sistema**:
    - Com a administrador, vull configurar la xarxa (IP, ports) per garantir la connectivitat del dispositiu.
    - Com a administrador, vull gestionar els usuaris i els seus permisos (grants) per controlar qui té accés a què.
- **Manteniment**:
    - Com a tècnic, vull veure l'estat dels discs durs per assegurar-me que les gravacions s'estan realitzant correctament.
    - Com a tècnic, vull descarregar els logs del sistema per diagnosticar possibles errors.

---

## 2. Diagrama de Casos d'Ús (Use Case Diagram)

### Actors:
1. **Operador**: Usuari amb permisos bàsics de visualització.
2. **Administrador**: Usuari amb control total sobre la configuració.
3. **Sistema Extern (NVR/Càmera)**: Proporciona els streams de vídeo i dades.

### Casos d'ús principals:
- **Sessió**: Login, Logout, Gestió de perfil.
- **Vídeo**: Veure càmeres en directe, Cercar gravacions, Control PTZ, Veure alarmes.
- **Configuració (Només Admin)**: Configurar xarxa, Configurar càmeres IP, Gestionar usuaris, Configurar entrades/sortides (I/O).
- **Manteniment**: Actualitzar Firmware, Consultar Logs, Generar Reports (Discs, Estat).

---

## 3. Diagrama de Classes (Class Diagram)

Aquest diagrama ha de reflectir les entitats de dades que hem trobat al codi (`api.model.ts`, `video.model.ts`).

### Entitats i Relacions principals:

| Classe | Atributs clau | Relacions |
| :--- | :--- | :--- |
| **Camera** | id, name, type, logicId | 1:N amb **Codec**, 1:N amb **Preset** |
| **Codec** | rtspUri, width, height, fps, kbps | Pertany a una **Camera** |
| **Preset** | name, token, position | Pertany a una **Camera** |
| **Alarm** | alarm_id, timestamp, priority, status | Relacionada amb **Camera** (per `camera_num`) |
| **User** | id, name, profile, grants | Gestionat pel sistema |
| **IoItem** | id, name, topic, type (Input/Output) | Representa hardware físic o virtual |
| **IpConfig** | address, mask, mac, port | Associat a **Camera** o al **Dispositiu** |

**Consells per al diagrama:**
- Usa **Composició** (rombe negre) entre `Camera` i `Codec` (un Codec no té sentit sense la seva càmera).
- Usa **Associació** entre `Alarm` i `Camera`.
- Les interfícies de TypeScript es poden dibuixar com a classes amb l'estereotip `<<interface>>`.

---

## 4. Diagrama de Seqüència (Sequence Diagram)

S'han de fer diagrames per als fluxos més importants. Aquí tens els passos detallats:

### Flux A: Login al sistema
1. `User` introdueix credencials a `LoginComponent`.
2. `LoginComponent` crida a `AuthService.login(user, pass)`.
3. `AuthService` fa una petició POST al backend.
4. El backend respon amb un token de sessió o `success`.
5. `AuthService` guarda l'estat i redirigeix a `HomePage`.

### Flux B: Visualització de vídeo en directe
1. `User` selecciona una càmera de la llista a `CamerasComponent`.
2. `CamerasComponent` obté la configuració de la càmera (`Camera` model).
3. `CamerasComponent` demana el stream URL (RTSP) al `VideoService`.
4. El `VideoService` connecta amb el servidor de streaming.
5. El reproductor de vídeo (HTML5/Websocket) comença a renderitzar els frames.

### Flux C: Canvi de configuració (amb Lock)
1. `Admin` accedeix a una pàgina de configuració.
2. El sistema demana un **Lock** (bloqueig) al backend per evitar que altres usuaris editin el mateix alhora.
3. Si el Lock s'obté, l'usuari pot editar. Si no, es mostra en mode lectura.
4. L'usuari modifica un camp (ex: IP) i prem "Guardar".
5. `ConfigService` envia el JSON i el token del Lock.
6. El backend valida, aplica els canvis i allibera el Lock.
7. El sistema notifica l'èxit i actualitza la UI.

---

## 5. Diagrama de Components / Arquitectura

Com que és un projecte **Angular**, l'arquitectura segueix aquest patró:

- **Core Layer**: Guàrdies d'autenticació, interceptors HTTP, serveis globals.
- **Shared Layer**: Components de UI reutilitzables (botons, taules, diàlegs).
- **Features (Main)**:
    - **Video Module**: Gestió de càmeres, reproductor, alarmes.
    - **Configuration Module**: Formularis complexos per configurar el hardware.
    - **Maintenance Module**: Logs, estats del sistema.
- **API Layer**: Comunicació amb el backend via REST i WebSockets per a dades en temps real.

---

## 6. Diagrama d'Estats (State Diagram)

Ideal per a l'entitat **Alarm**:
- **Estat Inicial**: `New` (quan arriba l'esdeveniment).
- **Transició**: L'usuari la veu -> `Read`.
- **Transició**: L'usuari la gestiona -> `Acknowledged` (si aplica).
- **Transició**: L'usuari l'arxiva -> `Archived`.
- **Estat Final**: `Deleted`.

---

## 7. Glossari de Termes per al TFG
- **PTZ**: Pan-Tilt-Zoom (control de moviment de càmera).
- **NVR**: Network Video Recorder (el dispositiu que grava).
- **RTSP**: Real Time Streaming Protocol.
- **I/O**: Inputs/Outputs (entrades i sortides digitals per a sensors i relés).
- **Grants**: Permisos específics d'un usuari.
