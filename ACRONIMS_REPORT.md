# Informe de Sigles Tècniques — Web Core View TFG
> Auditoria de tipografia acadèmica | Data: 22 de juny de 2026
> Fitxers analitzats: `capitols/1_introduccio.tex` … `capitols/8_conclusions.tex` + `main.tex`

---

## Resum executiu

| Estat | Sigles |
|-------|--------|
| ✅ Definides correctament en el primer ús | 17 |
| ⚠️ Usades abans de la seva definició formal | 8 |
| ❌ Mai definides formalment en prosa | 12 |

---

## BLOC 1 — Sigles ben definides (✅)

Definides al primer punt d'ús, amb l'expansió completa entre parèntesis.

| Sigla | Expansió | Primer ús i definició | Fitxer |
|-------|----------|-----------------------|--------|
| **TFG** | Treball de Final de Grau | Línia 7 | `1_introduccio.tex` |
| **NVR** | Network Video Recorder | Línia 7 ("videogravadors (\textit{Network Video Recorder}, d'ara endavant \textbf{NVR})") | `1_introduccio.tex` |
| **VMS** | Video Management System | Línia 29 ("VMS (\textit{Video Management System})") | `1_introduccio.tex` |
| **HEVC** / **AVC** | High Efficiency Video Coding / Advanced Video Coding | Línies 85-86 ("H.264 (AVC)" i "H.265 (HEVC)") | `1_introduccio.tex` |
| **SSE** | Server-Sent Events | Línia 114 ("SSE (\textit{Server-Sent Events})") | `1_introduccio.tex` |
| **JWT** | JSON Web Token | Línia 106 ("JWT (\textit{JSON Web Token})") | `1_introduccio.tex` |
| **XSS** | Cross-Site Scripting | Línia 106 ("atacs XSS (\textit{Cross-Site Scripting})") | `1_introduccio.tex` |
| **UX** | User Experience / Experiència d'usuari | Línia 4 ("experiència d'usuari (UX)") | `3_requisits.tex` |
| **SDP** | Session Description Protocol | Línia 118 ("negociació SDP (\textit{Session Description Protocol})") | `3_requisits.tex` |
| **CSRF** | Cross-Site Request Forgery | Línia 271 ("Cross-Site Request Forgery (CSRF)") | `3_requisits.tex` |
| **RGPD** | Reglament General de Protecció de Dades | Línia 279 | `3_requisits.tex` |
| **LOPDGDD** | Llei Orgànica 3/2018 de Protecció de Dades Personals i Garantia dels Drets Digitals | Línia 279 | `3_requisits.tex` |
| **AEPD** | Agència Espanyola de Protecció de Dades | Línia 296 | `3_requisits.tex` |
| **LPI** | Llei de Propietat Intel·lectual | Línia 304 | `3_requisits.tex` |
| **VFS** | Virtual File System | Línia 13 ("\textit{Virtual File System} (VFS)") | `4_arquitectura.tex` |
| **WAL** | Write-Ahead Logging | Línia 964 ("Write-Ahead Logging (WAL)") | `5_implementacio.tex` |
| **GPU** | Graphics Processing Unit | Línia 691 ("targeta gràfica (GPU)") | `5_implementacio.tex` |
| **SUS** (definició) | System Usability Scale | Línia 103 ("System Usability Scale (SUS)") | `6_validacio.tex` |
| **ACID** (definició) | Atomicitat, Consistència, Aïllament, Durabilitat | Línia 99 ("principis d'Atomicitat, Consistència, Aïllament i Durabilitat (ACID)") | `6_validacio.tex` |

---

## BLOC 2 — Sigles usades ABANS de la seva definició formal (⚠️)

Aquestes sigles apareixen en el text *prèviament* al punt on s'expandeixen. Cal moure la definició al primer ús, o afegir-la en parentètic en la primera aparició.

---

### ⚠️ HLS — HTTP Live Streaming

- **Capítol i línia del primer ús (sense definir):** `1_introduccio.tex`, línia 9
  - Context: *"protocols estàndard oberts (WebRTC, HLS, WSS)"* — usada en llista sense cap expansió.
- **Capítol i línia de la definició formal:** `1_introduccio.tex`, línia 71
  - Context: *"HLS (HTTP Live Streaming) \cite{hls_apple}"*
- **Risc:** 62 línies de distància dins del mateix capítol. Un lector lineal desconeix l'acrònim a la pàgina 1.

---

### ⚠️ WSS — WebSocket Segur

- **Capítol i línia del primer ús (sense definir):** `1_introduccio.tex`, línia 9
  - Context: *"protocols estàndard oberts (WebRTC, HLS, WSS)"*
- **Capítol i línia de la definició formal:** `1_introduccio.tex`, línia 115
  - Context: *"WebSockets (WSS)"*
- **Risc:** 106 línies de distància dins del mateix capítol. La sigla apareix 3 vegades abans de ser definida (línies 9, 11, 134).

---

### ⚠️ RTSP — Real-Time Streaming Protocol

- **Capítol i línia del primer ús (sense definir):** `1_introduccio.tex`, línia 43
  - Context: taula comparativa de mercat — columna "Protocol de vídeo: RTSP via ActiveX"
- **Capítol i línia de la definició formal:** `1_introduccio.tex`, línia 65
  - Context: *"RTSP (Real-Time Streaming Protocol)"*
- **Risc:** La taula apareix 22 línies abans de la definició en prosa. Risc baix (mateixa pàgina), però tipogràficament incorrecte.

---

### ⚠️ RTMP — Real-Time Messaging Protocol

- **Capítol i línia del primer ús (sense definir):** `1_introduccio.tex`, línia 43
  - Context: taula comparativa — columna "VMS (Milestone/Genetec): RTSP / RTMP"
- **Capítol i línia de la definició formal:** `1_introduccio.tex`, línia 66
  - Context: *"RTMP (Real-Time Messaging Protocol)"*
- **Risc:** Idèntic a RTSP. La taula precedeix la definició 23 línies.

---

### ⚠️ CORS — Cross-Origin Resource Sharing

- **Capítol i línia del primer ús (sense definir):** `1_introduccio.tex`, línies 121 i 123
  - Context: *"fent necessaris encapçalaments CORS complexos"* / *"elimina el problema CORS de manera estructural"*
- **Capítol i línia de la definició formal:** `4_arquitectura.tex`, línia 144
  - Context: *"problemes de CORS (Cross-Origin Resource Sharing)"*
- **Risc:** Usat al Capítol 1 sense definir. La definició apareix 3 capítols més tard. El lector del Capítol 1 llegeix CORS sense context.

---

### ⚠️ JWT (títol de subsecció)

- **Capítol i línia del primer ús (sense definir):** `1_introduccio.tex`, línia 104
  - Context: títol de subsecció *"Alternativa de mecanisme de sessió: JWT vs. Cookies HttpOnly/Secure"*
- **Capítol i línia de la definició formal:** `1_introduccio.tex`, línia 106
  - Context: *"\textbf{JWT (\textit{JSON Web Token})}"*
- **Risc:** Molt baix — 2 línies de distància. El títol de secció usa la sigla sense expansió, però la definició apareix immediatament a continuació. És recomanable afegir la forma completa al títol si el document ho permet.

---

### ⚠️ ACID — Atomicitat, Consistència, Aïllament, Durabilitat

- **Capítol i línia del primer ús (parcial/parentètic):** `1_introduccio.tex`, línia 79
  - Context: *"motor de base de dades relacional \textit{serverless}, transaccional (ACID)"* — usada en parèntesi sense expandir.
- **Capítol i línia de la definició formal completa:** `6_validacio.tex`, línia 99
  - Context: *"principis d'Atomicitat, Consistència, Aïllament i Durabilitat (ACID)"*
- **Risc:** La sigla s'usa com a adjectiu parentètic al Capítol 1 sense explicar-ne el significat. La definició completa no apareix fins al Capítol 6.

---

### ⚠️ UI — User Interface / Interfície d'usuari

- **Capítol i línia del primer ús (sense definir):** `2_gestio.tex`, línia 144
  - Context: *"dedicant les últimes dues setmanes exclusivament al poliment de la UI"*
- **Capítol i línia de la definició formal:** `5_implementacio.tex`, línia 4
  - Context: *"interfície gràfica d'usuari (UI)"*
- **Risc:** Usat al Capítol 2 sense definir. La definició arriba 3 capítols més tard.

---

### ⚠️ SUS — System Usability Scale (ús intern al mateix capítol)

- **Capítol i línia del primer ús (sense definir):** `6_validacio.tex`, línia 12
  - Context: *"usabilitat percebuda mitjançant el qüestionari SUS"* — primera aparició dins de la llista numerada.
- **Capítol i línia de la definició formal:** `6_validacio.tex`, línia 103
  - Context: *"System Usability Scale (SUS)~\cite{brooke1996_sus}"*
- **Risc:** 91 línies dins del mateix capítol. Menor però present. Recomanable moure la definició a la línia 12.

---

## BLOC 3 — Sigles mai definides formalment en prosa (❌)

Aquestes sigles apareixen al document però **en cap lloc** el text expandeix la sigla entre parèntesis en prosa corrent. Cal afegir la definició en el primer punt d'ús.

---

### ❌ OWASP — Open Web Application Security Project

- **Primera aparició:** `1_introduccio.tex`, línia 108 — *"recomanacions de seguretat de l'OWASP"*
- **Capítols d'ús:** Ch.1 (línia 108, 133), Ch.4 (línia 140, línia de comentari NGINX), Ch.8 (línia 27)
- **Diagnòstic:** Usat 5 vegades assumint que el lector coneix l'organisme. Cap expansió en cap punt del document.
- **Proposta de correcció:** A `1_introduccio.tex`, línia 108, afegir: *"recomanacions de seguretat de l'\textbf{OWASP} (\textit{Open Web Application Security Project})"*

---

### ❌ DTLS — Datagram Transport Layer Security

- **Primera aparició:** `1_introduccio.tex`, línia 70 — *"transport sobre UDP sota xifratge DTLS"*
- **Capítols d'ús:** Ch.1 (línia 70), Ch.3 (línia 290), Ch.7 (línia 87)
- **Diagnòstic:** Sempre usat com a sigla nua. Ni la cita RFC3711 ni cap text expandeix l'acrònim.
- **Proposta de correcció:** A `1_introduccio.tex`, línia 70, substituir *"DTLS"* per *"DTLS (\textit{Datagram Transport Layer Security})"*

---

### ❌ SRTP — Secure Real-time Transport Protocol

- **Primera aparició:** `1_introduccio.tex`, línia 140 — *"WebRTC SRTP/UDP"*
- **Capítols d'ús:** Ch.1 (línia 140), Ch.4 (línies 16, 67), Ch.7 (línies 65, 87)
- **Diagnòstic:** Ch.4, línia 16 inclou la cita `\cite{rfc3711}` però no expandeix la sigla en prosa. Usat 5 vegades sense definició.
- **Proposta de correcció:** A `1_introduccio.tex`, línia 140, afegir la primera aparició com *"SRTP (\textit{Secure Real-time Transport Protocol})/UDP"*

---

### ❌ ICE — Interactive Connectivity Establishment

- **Primera aparició:** `4_arquitectura.tex`, línia 16 — *"negociat per ICE~\cite{rfc8445}"*
- **Capítols d'ús:** Ch.4 (línies 16, 47), Ch.5 (línia 694), Ch.7 (línia 44, 68)
- **Diagnòstic:** La cita RFC8445 és present però la sigla no es mai expandida en prosa.
- **Proposta de correcció:** A `4_arquitectura.tex`, línia 16, substituir *"ICE~\cite{rfc8445}"* per *"ICE (\textit{Interactive Connectivity Establishment})~\cite{rfc8445}"*

---

### ❌ WHEP — WebRTC HTTP Egress Protocol

- **Primera aparició (text real):** `5_implementacio.tex`, línia 691 — *"WebRTC i WHEP~\cite{rfc9716}"*
- **Única expansió existent:** `5_implementacio.tex`, línia 711 — però **dins d'un bloc comentat** (`%`) que LaTeX no processa.
- **Diagnòstic:** La definició formal existeix, però en un comentari LaTeX invisible al document imprès. L'únic WHEP visible per al lector queda sense explicació.
- **Proposta de correcció:** Moure l'expansió del comentari al text corrent de la línia 691: *"\textbf{MediaMTX} (actuant com a servidor centralitzat de WebRTC i TEXTBF{WHEP} (\textit{WebRTC HTTP Egress Protocol})~\cite{rfc9716})"*

---

### ❌ ONVIF — Open Network Video Interface Forum

- **Primera aparició:** `1_introduccio.tex`, línia 158 — *"detecció automàtica de càmeres IP per ONVIF"*
- **Capítols d'ús:** Ch.1 (línia 158), Ch.5 (línies 482, 483 —valors JSON—, línia 642 amb `\cite{onvif_spec}`)
- **Diagnòstic:** Ch.5 inclou la cita bibliogràfica però cap expansió de la sigla en prosa.
- **Proposta de correcció:** A `1_introduccio.tex`, línia 158, substituir *"per ONVIF"* per *"per \textbf{ONVIF} (\textit{Open Network Video Interface Forum})"*

---

### ❌ TLS — Transport Layer Security

- **Primera aparició:** `4_arquitectura.tex`, línia 38 — *"Gestiona el xifratge TLS (protocols TLSv1.2 i TLSv1.3)"*
- **Capítols d'ús:** Ch.4 (línies 38, 136), Ch.7 (línia 87)
- **Diagnòstic:** Usada com a sigla coneguda. La forma parentètica *(protocols TLSv1.2...)* descriu les versions però no expandeix l'acrònim.
- **Proposta de correcció:** A `4_arquitectura.tex`, línia 38, afegir *"TLS (\textit{Transport Layer Security})"* en el primer ús.

---

### ❌ SPA — Single-Page Application

- **Primera aparició:** `4_arquitectura.tex`, línia 63 — *"Descàrrega de l'aplicació Angular (SPA)"*
- **Capítols d'ús:** Ch.4 (línies 63, 81 —en comentari del codi NGINX—)
- **Diagnòstic:** La sigla s'usa com a parentètic però sense expandir-la. La forma completa *Single-Page Application* no apareix en cap lloc del document.
- **Proposta de correcció:** Substituir el parentètic per *"(SPA, \textit{Single-Page Application})"* a `4_arquitectura.tex`, línia 63.

---

### ❌ gRPC

- **Primera aparició:** `2_gestio.tex`, línia 61 — *"protocol gRPC"*
- **Capítols d'ús:** Ch.2 (línia 61), Ch.4 (línies 13, 15, 21, 23), Ch.5 (línies 908, 912, 953, 961), Ch.7 (línies 48, 62, 93)
- **Diagnòstic:** Usada extensament sense cap nota explicativa. Ch.7, línia 93 inclou `\cite{grpc_io}` però sense expansió en prosa. gRPC és un nom propi de Google però l'expansió (Google Remote Procedure Call) és acadèmicament convenient.
- **Proposta de correcció:** A `2_gestio.tex`, línia 61 (primer ús), afegir una nota: *"protocol \textbf{gRPC} (\textit{Google Remote Procedure Call})~\cite{grpc_io}"*

---

### ❌ SOLID — Principis de disseny de software

- **Primera aparició:** `2_gestio.tex`, línia 20 — *"principis SOLID \cite{solid_principles}"*
- **Capítols d'ús:** Ch.2 (línia 20), Ch.7 (línia 32), Ch.8 (línia 56)
- **Diagnòstic:** Té cita bibliogràfica però l'acrònim no s'expandeix mai. SOLID = Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion.
- **Proposta de correcció:** A `7_integracio_coneixements.tex`, línia 32 (on s'explica amb més detall), afegir una nota breu sobre l'acrònim.

---

### ❌ UML — Unified Modeling Language

- **Primera aparició:** `7_integracio_coneixements.tex`, línia 32 — *"diagrames UML (seqüència, activitat i classes)"*
- **Capítols d'ús:** Ch.7 (línia 32 únicament)
- **Diagnòstic:** La forma parentètica descriu els tipus de diagrama però no expandeix la sigla.
- **Proposta de correcció:** Substituir per *"diagrames \textbf{UML} (\textit{Unified Modeling Language}: seqüència, activitat i classes)"*

---

### ❌ SSL — Secure Sockets Layer

- **Primera aparició:** `4_arquitectura.tex`, línia 38 — *"ports \texttt{60080} (HTTP) i \texttt{60443} (HTTPS/SSL)"*
- **Capítols d'ús:** Ch.4 (línies 38, 163 —`shared:SSL:10m`— en blocs de codi)
- **Diagnòstic:** Apareix en context de codi NGINX (`ssl_session_cache`) i en referència de port sense expansió. Com que SSL és tècnicament substituïda per TLS, caldria una nota breu.
- **Proposta de correcció:** Al primer ús en prosa, afegir: *"HTTPS/SSL (\textit{Secure Sockets Layer}, avui substituïda per TLS)"* o simplement suprimir "SSL" i deixar "HTTPS/TLS".

---

## BLOC 4 — Sigles de protocols HTTP i xarxa d'ús molt comú

Les sigles següents no es defineixen al document, però es consideren **tàcitament conegudes** en un TFG d'Enginyeria Informàtica. No requereixen correcció obligatòria, però es llistene per completar l'auditoria:

| Sigla | Expansió | Primera aparició |
|-------|----------|-----------------|
| **HTTP** | HyperText Transfer Protocol | `1_introduccio.tex`, línia 67 |
| **HTTPS** | HTTP Secure | `4_arquitectura.tex`, línia 38 |
| **TCP** | Transmission Control Protocol | `1_introduccio.tex`, línia 115 |
| **UDP** | User Datagram Protocol | `1_introduccio.tex`, línia 70 |
| **API** | Application Programming Interface | `2_gestio.tex`, línia 61 |
| **REST** | Representational State Transfer | `2_gestio.tex`, línia 61 |
| **URL** | Uniform Resource Locator | `4_arquitectura.tex` |
| **SQL** | Structured Query Language | `7_integracio_coneixements.tex`, línia 38 |
| **RAM** | Random Access Memory | `2_gestio.tex`, línia 59 |
| **CPU** | Central Processing Unit | `1_introduccio.tex`, línia 80 |
| **IP** | Internet Protocol | Múltiples capítols |

---

## Prioritats de correcció recomanades

### Prioritat ALTA (afecten la comprensió del lector):
1. **OWASP** — usada 5 vegades, mai definida, poc coneguda fora del sector de seguretat
2. **DTLS** — protocol crític per a WebRTC, usat 3 vegades sense definir
3. **SRTP** — protocol crític per a WebRTC, usat 5 vegades sense definir
4. **WHEP** — la seva única definició és en un comentari LaTeX invisible al PDF
5. **ICE** — protocol de WebRTC, usat 5 vegades sense definir

### Prioritat MITJANA (inconsistència tipogràfica):
6. **CORS** — usada al Ch.1 sense definir, definida al Ch.4
7. **ACID** — usada parentèticament al Ch.1, definida al Ch.6
8. **UI** — usada al Ch.2, definida al Ch.5
9. **HLS** / **WSS** — usades al paràgraf introductori del Ch.1, definides 60-106 línies després
10. **SPA** — mai expandida en prosa

### Prioritat BAIXA (estilística, no crítiques):
11. **SUS** — definida 91 línies tard dins del mateix capítol
12. **gRPC** — nom propi; acceptable sense expansió si hi ha cita
13. **UML**, **SOLID** — molt coneguts al grau; acceptable amb cita
14. **SSL**, **TLS** — protocols de seguretat molt coneguts
15. **RTSP**, **RTMP** — definides al mateix capítol, 22-23 línies tard (problema de taules LaTeX)

---

*Informe generat automàticament per auditoria QA tipogràfica. Cap fitxer .tex ha estat modificat.*
