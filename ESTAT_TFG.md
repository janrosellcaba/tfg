# Estat del TFG — Web Core View
> Actualitzat: juny 2026. Basat en auditoria completa de tots els capítols i docs/.

---

## Resum executiu

La memòria del TFG està **pràcticament completa**. Tots els 8 capítols i l'annex estan escrits amb contingut substantiu. El capítol 5 (Implementació) és excepcionalment detallat: codi real d'Angular, JSON d'API, fragments de C++ del backend i diagrames referenciats. No existeix cap capítol buit.

---

## Estat per capítol

| Capítol | Títol | Estat | Notes |
|---|---|---|---|
| 1 | Introducció i Context | ✅ COMPLET | Problema, estat de l'art, justificació alternatives, objectius |
| 2 | Gestió del Projecte | ✅ COMPLET | Kanban, Gantt, pressupost real (15.128,75€), sostenibilitat |
| 3 | Especificació de Requisits | ✅ COMPLET | User stories, RF/RNF, casos d'ús, marc normatiu (RGPD) |
| 4 | Arquitectura i Disseny | ✅ COMPLET | Microserveis, NGINX, Schema-driven, model de dades |
| 5 | Implementació | ✅ COMPLET | Login, Home, Config, WebRTC, HLS, Alarmes, Manteniment |
| 6 | Validació i Proves | ✅ COMPLET | Jasmine/Karma, Playwright, latències, CPU/memòria, UAT |
| 7 | Integració de Coneixements | ✅ COMPLET | Competències FIB, assignatures relacionades |
| 8 | Conclusions i Treball Futur | ✅ COMPLET | Assoliment d'objectius, treball futur, reflexions personals |
| Annexos | Annexos | ✅ COMPLET | Annex A.1 corregit (juny 2026): format JSON real amb underscores |

---

## Correccions pendents

### 1. Annex A.1 — Format JSON incorrecte ✅ RESOLT (juny 2026)
~~**Problema:** L'Annex A.1 usa format JSON Schema estàndard (`"type"`, `"minimum"`, `"enum"`) però el format real que envia `la-core-legacy` és amb **underscores prefixats** (`"_type"`, `"_min"`, `"_allowedValues"`).~~

**Correcció aplicada:** L'Annex A.1 ara mostra el format real: diccionari pla amb claus en notació de punts (`"camera.{id}.stream.codec"`) i metadades underscore-prefixades (`_type`, `_allowedValues`, `_min`, `_max`, `_default`, `_description`). Text explicatiu actualitzat per justificar la convenció dels underscores. Compilació verificada (100 pàgines, sense errors).

### 2. Mòdul "Reports" — eliminar del menú ⚠️
**Problema:** La secció 5.3 (Home Page) llista "Reports" al menú de Manteniment però no té secció d'implementació corresponent.

**Acció:** Eliminar "Reports" de la llista del menú. No es documenta al TFG per no afegir complexitat.

### 3. Contradicció sobre sessió (menor) ℹ️
**Problema:** El capítol 3 diu "No es permet l'ús de galetes; s'ha d'usar LocalStorage". El capítol 5 implementa cookies HttpOnly/Secure via NGINX i Signal en memòria (zero-persistence, sense LocalStorage).

**Acció:** Corregir el capítol 3: s'eviten cookies llegibles per JavaScript (`document.cookie`), però s'usen cookies `HttpOnly` gestionades exclusivament per NGINX, immune a XSS. El LocalStorage tampoc s'usa.

### 4. FS2 — afegir paràgraf breu (millora) ℹ️
**Oportunitat:** La secció de gravacions del capítol 5 menciona `fs2_file_read_buffer` sense explicar el concepte.

**Acció:** Afegir un paràgraf breu explicant que FS2 és un sistema de fitxers propietari de Lanaccess que opera sobre la partició raw del disc dur (bypassa ext4), garantint escriptura atòmica i rendiment d'alt débit per a fluxos continus de vídeo.

---

## Arquitectura tècnica — dades clau per a futura redacció

### Sessió
- Cookies `HttpOnly` + `Secure` gestionades per NGINX (no JavaScript).
- Estat de sessió: Signal privat en memòria (zero-persistence). F5 = logout. Comportament intencional.

### WebRTC — dues connexions separades
- **Senyalització SDP (WHEP):** HTTP POST via NGINX → `/video-engine/stream/<camera_id>/whep`.
- **Flux de vídeo (SRTP/UDP):** Connexió directa peer-to-peer, bypassa NGINX. Ports 10000–20000 (ICE dinàmic).
- En LAN no cal STUN/TURN (Host Candidates). Per NAT extern caldria STUN/TURN extern.

### Schema-Driven — format real
- Underscores prefixats: `_type`, `_min`, `_max`, `_regex`, `_allowedValues`, `_description`, `_default`.
- L'Annex A.1 usa JSON Schema estàndard — és incorrecte, cal corregir.

### FS2
- Sistema de fitxers propietari Lanaccess sobre partició raw (no ext4).
- Escriptura en blocs físics seqüencials de mida fixa. Atòmica en cas de tall elèctric.

### WebSocket topology
| Canal | Path | Events | Obert quan |
|---|---|---|---|
| Global | `/ws/la-core-notifier/` | `ERRORS_NOTIFY`, `IO_NOTIFY`, `SOFTWARE_UPDATE_NOTIFY` | Des del login, persistent |
| Alarmes | `/ws/la-core-alarm/` | `DB_ENGINE_ALARM_NOTIFY`, `ALARMS_GET`, `ALARM_READ` | Només a la pàgina d'alarmes |

El badge de notificació (campana) prové de `la-core-notifier` (ERRORS_NOTIFY), no de `la-core-alarm`.

### Config Lock
- `expiresIn`: 30 s o 120 s (depenent del node). Renovació cada `expiresIn/2`, fora de Zone.js.
- Auto-alliberament backend quan expira si client perd connexió.
- Forçar alliberament: `/unlock` amb prioritat. Admin pot robar lock amb `force=true`.

### Firmware update — seqüència
1. Upload .bin via HTTP POST
2. Polling `/firmware_upgrade_state` (UPLOADING → UPDATING → REBOOTING)
3. D-Bus → `la-core-notifier` → event `SOFTWARE_UPDATE_NOTIFY` → tots els clients redirigits
4. Cooldown 15 s → polling `connectivity.cgi` fins que l'NVR respon

### Web Proxy
1. Angular → la-core-legacy: IP + port de la càmera
2. la-core-legacy obre túnel a `127.0.0.1:port_dinàmic`
3. NGINX ruta `/camera/<camera_id>/` → loopback port
4. Angular renderitza `<iframe src="/camera/<camera_id>/">`

### i18n
- Idiomes: EN (per defecte), ES, FR — tots completament traduïts.
- Llibreria: `ngx-translate`. Fallback: 'en'.

---

## Figures disponibles a `/figures/`

**Diagrames tècnics:** `arquitectura_serveis.png`, `angular_components.png`, `angular_modules.png`, `deployment_diagram.png`, `class_diagram.png`, `seq_login.png`, `seq_config_lock.png`, `seq_video_streaming.png`, `activity_video_connection.png`, `alarms_data_flow.png`, `state_alarm.png`, `use_cases.png`, `user_journey.png`, `gantt.png`, `stack_mindmap.png`

**Pantalles UI:** `ui_login.png`, `ui_home.png`, `ui_home_info.png`, `ui_home_user.png`, `ui_errors.png`, `ui_cameras.png`, `ui_recordings.png`, `ui_alarms.png`, `ui_system1.png`, `ui_system2.png`, `ui_system3.png`, `ui_configuration.png`, `ui_proxy.png`, `ui_wizards.png`, `ui_update.png`, `ui_updating1.png`, `ui_updating2.png`, `ui_updating3.png`, `ui_log.png`, `ui_license.png`, `ui_documentation.png`
