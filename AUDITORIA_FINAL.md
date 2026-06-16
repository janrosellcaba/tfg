# AUDITORIA FINAL — Web Core View (TFG, Jan Rosell Caba)
> Rol auditor: Tribunal d'Enginyeria del Software + Arquitecte de Sistemes Sènior
> Document analitzat: `main.pdf` (104 pàgines)
> Data auditoria: 2026-06-16

---

## ÍNDEX DE TROBALLES PER PRIORITAT

| ID | Prioritat | Lent | Capítol | Resum breu |
|----|-----------|------|---------|------------|
| A-01 | **CRÍTICA** | Arquitectura | 5.10.2 | VFS de SQLite encadena la-db-engine → la-video-engine sense SPOF mitigation |
| A-02 | **ALTA** | Arquitectura | 4.1.3, 6.3.1 | Eliminació de Zone.js afirmada però desmentida pel propi codi (Listing 5.11) |
| A-03 | **ALTA** | Arquitectura | 5.3.3 | Config Lock Token enviat per query parameter, contradiu les garanties OWASP |
| A-04 | **ALTA** | Arquitectura | 4.1.3 | Timeout de 5 s per fallback ICE no explicat tècnicament: qui el gestiona? |
| A-05 | **ALTA** | Arquitectura | 5.10.2 | Destí físic del fitxer SQLite (FS2 raw o ext4) mai especificat |
| G-01 | **ALTA** | Gestió | 2.3.4 | Causalitat trencada: H.265 complex ≠ justificació per pivotar a single-NVR |
| G-02 | **ALTA** | Gestió | 2.2.6 / 5.9 | Milestone reporta HLS al 60%; el capítol 5 el presenta com a complet sense update |
| G-03 | **ALTA** | Gestió | 2.3.1 | Total hores final = 540 (igual que estimació), estadísticament sospitós |
| R-01 | **ALTA** | Rigor | 6.3.1 | Reducció 35% CPU: baseline inexistent, metodologia insuficient |
| R-02 | **ALTA** | Rigor | 6.5 | SUS "adaptat" no és SUS estàndard; benchmarks de 85.6 no s'apliquen |
| A-06 | **MITJANA** | Arquitectura | 4.1 / Taula 4.1 | Matriu de comunicació omet D-Bus i gRPC entre microserveis interns |
| A-07 | **MITJANA** | Arquitectura | 5.8 / 4.1 | MediaMTX introduït al cap.5 sense aparèixer al diagrama d'arquitectura (Fig. 4.1) |
| A-08 | **MITJANA** | Arquitectura | 5.9.2 vs Listing 5.16 | URL de HLS inconsistent: `/hls-play.m3u8` vs `/video-engine/hls-play.m3u8` |
| A-09 | **MITJANA** | Arquitectura | 5.3.2 | "Zero-persistence" (F5 tanca sessió) és fals: la cookie HttpOnly sobreviu el refresh |
| A-10 | **MITJANA** | Arquitectura | 5.9.4 | Injecció SPS/PPS/VPS per H.265: cas de canvi de còdec mid-recording no adreçat |
| A-11 | **MITJANA** | Arquitectura | 5.8.2 / Listing 5.13 | RTCPeerConnection config (ICE servers / STUN/TURN) mai mostrada ni descrita |
| G-04 | **MITJANA** | Gestió | 1.2 / 8.2 | Multi-NVR apareix com a objectiu original, pivot estratègic i treball futur simultàniament |
| R-03 | **MITJANA** | Rigor | 6.4 | "Lògica 5.6" referenciada al text no existeix al document (referència trencada) |
| R-04 | **MITJANA** | Rigor | 6.2 | Cobertura >85% sense especificar tipus (statement/branch/function), fitxers ni nombre de tests |
| R-05 | **MITJANA** | Rigor | 5.9.1 | Contribució a FS2 ambigua: la funció `fs2_file_read_buffer` és de Lanaccess o del projectista? |
| R-06 | **MITJANA** | Rigor | Listing 4.1 | `unsafe-inline` en CSP style-src presentat com a mesura de seguretat sense cap advertència |
| R-07 | **MITJANA** | Rigor | 3.3.1 / 6.3 | Latència màxima mesurada (300 ms) coincideix amb el límit del requisit; no es distingeix el p95 |
| R-08 | **MITJANA** | Rigor | 5.8.3 | "Patró Strategy d'Angular" — el Strategy és GoF, no un patró Angular |
| A-12 | **BAIXA** | Arquitectura | 2.2.4 | "Buffer d'Angular" per H.265 és conceptualment incorrecte; el buffer el gestiona HLS.js |
| A-13 | **BAIXA** | Arquitectura | 5.8.1 | Drag-and-drop "nativa" no referencia cap biblioteca ni mòdul Angular CDK |
| G-05 | **BAIXA** | Gestió | 6.5 | UAT amb 8 participants: no s'admet la limitació estadística de la mostra |
| R-09 | **BAIXA** | Rigor | 1.4.1 | "batteries-included" en anglès dins text acadèmic en català sense glossa |
| R-10 | **BAIXA** | Rigor | 1.3.2 | Taula 1.1 — fonts "Elaboració pròpia" però les files d'ActiveX no citen mesures |
| R-11 | **BAIXA** | Rigor | Global | Termes "descodificació" i "descompressió" usats indistintament; no equivalents |
| R-12 | **BAIXA** | Rigor | 5.8 | GStreamer introduït al cap.5 sense aparèixer al mapa tecnològic (Fig. 2.1) ni al cap.4 |

---

## LENT 1: INCONSISTÈNCIES ARQUITECTÒNIQUES I TÈCNIQUES

### A-01 — CRÍTICA | Capítol 5.10.2
**Single Point of Failure en la cadena de persistència d'alarmes**

La cadena d'escriptura d'alarmes és: `la-core-alarm` → gRPC → `la-db-engine` → VFS personalitzat → Unix Domain Socket `/tmp/io.sock` → `la-video-engine` → disc físic.

Això significa que **si `la-video-engine` cau o es bloqueja, tota la persistència d'alarmes falla silenciosament**. En un sistema de seguretat crítica (24/7), `la-video-engine` és el servei més carregat del sistema (processa vídeo en temps real), i es converteix en el coll d'ampolla de la base de dades d'alarmes. El document no descriu cap mecanisme de fallback, cua persistent, o timeout per a les operacions del socket. Tampoc inclou aquest risc a la matriu de riscos (Taula 2.2).

**Pregunta del tribunal esperada:** *"Si la-video-engine es bloqueja durant 30 segons processant un segon H.265 complex, quantes alarmes es perden i com es recuperen?"*

---

### A-02 — ALTA | Capítols 4.2.2 i 6.3.1
**Eliminació de Zone.js afirmada però contraditada pel codi**

Secció 4.2.2: *"l'ús de Signals permet que l'aplicació s'apropi a un model Zone-less"* (la paraula "apropi" és correcta i prudent).

Però secció 6.3.1: **"Gràcies a l'eliminació de les comprovacions de cicle complet de Zone.js en Angular 19"** — aquí ja s'usa "eliminació", que és facticialment incorrecte.

**Prova directa al propi document:** Listing 5.11 (Secció 5.5.5) mostra:
```typescript
this.zone.runOutsideAngular(() => {
  this.renewalTimer = setTimeout(() => {
    this.zone.run(() => this.acquireLock());
  }, ms);
});
```
L'ús explícit de `NgZone` demostra que Zone.js **no ha estat eliminat** — simplement s'evita per a temporitzadors específics. El mode zoneless real d'Angular 19 requereix `bootstrapApplication` amb `provideExperimentalZonelessChangeDetection()`, que no es descriu en cap punt de la memòria.

**Impacte:** La reducció del 35% de CPU (Secció 6.3.1) es basa parcialment en una premissa incorrecta, cosa que debilita la credibilitat del resultat de rendiment.

---

### A-03 — ALTA | Secció 5.3.3
**Config Lock Token transmès per query parameter: contradiu el model de seguretat OWASP**

La secció 3.7 descriu amb detall les mesures de seguretat per evitar XSS i CSRF: cookies HttpOnly, no LocalStorage, Same-Origin. El sistema és presentat com a OWASP-compliant.

Però Listing 5.6 (authInterceptor) fa:
```typescript
const modifiedReq = req.clone({ setParams: { token: token } });
```

Posar el token de blocatge en un **query parameter URL** implica que:
1. Apareix en els **logs d'accés de NGINX** (en clar)
2. Queda emmagatzemat a l'**historial del navegador**
3. Pot aparèixer a la capçalera **Referer** en peticions cross-origin
4. Visible a qualsevol proxy o sistema de logging intermedi

Aquesta pràctica contradiu directament les garanties de seguretat descrites a la Secció 3.7 i al punt 4.1.2 (Seguretat Defensiva OWASP), on es presenta la plataforma com a immune a robatori de tokens. El token de Config Lock hauria d'anar en un header HTTP personalitzat (`X-Config-Lock-Token`) o al body de la petició.

---

### A-04 — ALTA | Secció 4.1.3
**Timeout de 5 s per fallback ICE: qui el gestiona i com?**

La secció 4.1.3 indica: *"Si el navegador detecta que l'estat de la connexió ICE transita cap a `failed` o `disconnected` després d'un timeout de seguretat de 5 segons, s'activa automàticament la lògica de degradació de servei."*

Problemes tècnics:
- L'estat `failed` en ICE es produeix **després** que tots els candidats s'hagin exhaurit, procés que per defecte pot trigar **30-40 segons** en navegadors moderns (controlat per l'agent ICE intern del browser, no per Angular).
- L'estat `disconnected` és **transitori** i pot recuperar-se sol; no és la senyal correcta per disparar un fallback definitiu.
- El document no mostra el codi que implementa aquest timeout de 5 s ni com s'intercepta l'`iceConnectionState` per forçar el canvi.

La Secció 6.4 (proves de robustesa) no inclou cap test específic del fallback WebRTC → HLS en condicions de firewall UDP bloquejat.

---

### A-05 — ALTA | Secció 5.10.2
**Destí físic de la base de dades SQLite mai especificat**

La Secció 5.9.1 explica que el sistema FS2 opera "directament sobre la partició raw del disc dur de l'NVR, sense intermediació del sistema de fitxers del nucli Linux (ext4)."

La Secció 5.10.2 descriu que el driver VFS personalitzat (`remote_vfs`) redirigeix les escriptures de SQLite a través de `la-video-engine`. Però **mai s'especifica si la base de dades d'alarmes rau dins del sistema FS2 (raw partition) o en la partició ext4 del sistema operatiu**.

Si és en FS2: com es gestiona l'espai? FS2 està dissenyat per a fitxers de vídeo contigus de mida fixa. SQLite opera amb pàgines de 4KB i pot fragmentar-se.
Si és en ext4: per què cal el VFS personalitzat i el routing per `la-video-engine`? L'accés directe ext4 des de `la-db-engine` seria més simple.

Aquesta ambigüitat és arquitectònicament crítica i no permet avaluar la viabilitat real del disseny de persistència.

---

### A-06 — MITJANA | Taula 4.1
**Matriu de comunicació incompleta: D-Bus i gRPC interns omesos**

La Taula 4.1 ("Matriu de comunicació de la plataforma") descriu 5 files de comunicació totes amb el frontend o NGINX com a origen. La comunicació interna entre microserveis **no apareix**:
- `la-core-alarm` → `la-db-engine` (gRPC, descrit a 5.10.1)
- `la-video-engine` ↔ tots els serveis (D-Bus, descrit a 4.1)
- `la-db-engine` → `la-video-engine` (Unix Domain Socket, descrit a 5.10.2)

Sense aquesta visió completa, la taula és enganyosa i dona la impressió que tota la comunicació passa per NGINX.

---

### A-07 — MITJANA | Secció 5.8 i Figura 4.1
**MediaMTX introduït al capítol 5 sense aparèixer a l'arquitectura del capítol 4**

La Figura 4.1 ("Relació i comunicació entre els serveis de l'NVR") mostra: web-core-view, core-legacy, dbus, core-alarm, core-notifier, db-engine, SQLite3. **No apareix MediaMTX ni GStreamer**.

La Secció 5.8 introdueix GStreamer i MediaMTX com a components fonamentals del pipeline de vídeo: *"el servidor multimèdia d'alt rendiment MediaMTX (actuant com a servidor centralitzat de WebRTC i WHEP)."*

Si MediaMTX és un procés separat (molt probable, donat que és un projecte open-source independent), llavors el diagrama d'arquitectura de la Figura 4.1 és incomplet. Si MediaMTX és integrat dins de `la-video-engine`, cal especificar-ho. La relació entre `la-video-engine` i MediaMTX (procés fill? wrapper? API interna?) mai s'explica.

---

### A-08 — MITJANA | Secció 5.9.2 vs Listing 5.16
**URL del servidor HLS inconsistent entre text i codi**

Secció 5.9.2 (text): *"el client demana la llista de reproducció realitzant una crida HTTP GET cap a la ruta `/hls-play.m3u8?token=...&start=...&end=...`"*

Listing 5.16 (codi): `const hlsUrl = '/video-engine/hls-play.m3u8?token=${token}&start=${start}&end=${end}';`

La URL del codi **inclou el prefix `/video-engine/`** (necessari per al routing de NGINX cap a port 8082), però el text descriptiu l'omet. En un tribunal, un examinador pot preguntar si la URL funciona sense el prefix o si el text és incorrecte.

---

### A-09 — MITJANA | Secció 5.3.2
**"Zero-persistence: F5 tanca la sessió" — afirmació tècnicament incorrecta**

El document afirma reiteradament que un refresc de pàgina (F5) "tanca la sessió" del sistema. Però el mecanisme real és:
- La cookie de sessió HttpOnly gestionada per NGINX **persisteix** en el navegador en un refresc (session cookies s'esborren en tancar el navegador, no en refrescar la pàgina).
- El que es perd és l'**estat en memòria d'Angular** (el Signal privat).
- En prémer F5, l'app angular es reinicia i mostra la pantalla de login, però la **cookie de sessió del servidor encara és vàlida**.

Conseqüència: Si un usuari refresca i torna a fer login, es crearia una nova sessió sobre una cookie existent potencialment vàlida. El document no descriu el comportament del backend en aquest cas ni confirma si `PlainLogin` invalida sessions prèvies.

La frase *"comportament intencionat per a entorns de seguretat industrial"* presenta una limitació tècnica (pèrdua d'estat JavaScript) com si fos una característica de seguretat deliberada del servidor. Això és misleading.

---

### A-10 — MITJANA | Secció 5.9.4
**H.265 SPS/PPS/VPS: cas de canvi de còdec mid-recording no adreçat**

La lògica descrita és: si el segment actual no té capçaleres IDR, *"el servidor retrocedeix de forma lògica a l'índex del primer buffer del fitxer de gravació de la càmera per buscar les capçaleres de referència."*

Casos no contemplats:
1. **Canvi de resolució o bitrate** durant la gravació (possible en gravació adaptativa): les capçaleres SPS del primer buffer poden ser invalides per als segments posteriors.
2. **Reinici del gravador** en mig d'una gravació: el primer buffer del fitxer pot no tenir capçaleres vàlides per als segments post-reinici.
3. **Gravació de múltiples fitxers FS2** que formen una sessió contínua: el "primer buffer" d'un fitxer secundari pot no tenir capçaleres SPS.

Aquests casos d'edge en gravació d'alta resolució (4K H.265 multi-càmera) podrien causar congelació del reproductor sense missatge d'error.

---

### A-11 — MITJANA | Secció 5.8.2
**Configuració ICE servers (STUN/TURN) mai descrita ni mostrada**

Listing 5.13: `const pc = new RTCPeerConnection(this.config);`

L'objecte `this.config` mai és mostrat. En xarxes corporatives (el target principal del producte), WebRTC requereix servidors STUN per a NAT traversal i potencialment TURN per a entorns amb firewall estricte. El document menciona que el fallback a HLS s'activa si "els tallafocs bloquegen el trànsit WebRTC", però no explora TURN com a alternativa (que mantindria la baixa latència de WebRTC inclús a través de firewalls TCP).

Donat que la proposta de valor principal és WebRTC <300ms vs HLS 2-5s, no esgotar TURN com a opció intermèdia representa un gap de disseny significatiu per a entorns corporatius.

---

### A-12 — BAIXA | Secció 2.2.4
**"Buffer d'Angular" per H.265: terminologia conceptualment incorrecta**

El document descriu la desviació temporal del H.265 com: *"ha requerit un buffer d'Angular molt més complex per garantir la fluïdesa."*

Angular no gestiona buffers de vídeo. El buffering de segments HLS és responsabilitat de **HLS.js** (configurable via `maxBufferHole` i `manifestLoadingTimeOut`, com es veu correctament al Listing 5.16). Parlar de "buffer d'Angular" confon la capa de presentació amb la capa de streaming multimèdia.

---

### A-13 — BAIXA | Secció 5.8.1
**Drag-and-drop "nativa": falta referenciar la implementació**

*"La interfície implementa una lògica d'arrossegar i deixar anar (Drag and Drop) nativa integrada amb la llista lateral de càmeres."* No s'especifica si es fa servir l'Angular CDK `DragDropModule`, l'API nativa HTML5 Drag and Drop, o una solució pròpia. En un document d'implementació, aquesta omissió és notable.

---

## LENT 2: COHESIÓ DEL PRODUCTE I GESTIÓ

### G-01 — ALTA | Secció 2.3.4
**Causalitat trencada: complexitat H.265 ≠ justificació per pivotar a single-NVR**

La secció 2.3.4 descriu que quan s'assoleix el "Llindar Vermell (Desviació > 10%)", *"la mesura correctora clau ha estat la decisió de dissenyar una versió simplificada dirigida a petites instal·lacions (single-NVR)."*

Però el problema que va causar la desviació era:
1. Complejitat del buffer H.265
2. Complexitat del parseig del motor schema-driven

Ambdós problemes **afecten igualment una versió single-NVR i una versió multi-NVR**. La gestió de múltiples NVRs en paral·lel és una capa de nivell superior (UI de llista de dispositius, agregació d'estats) completament independent del codec H.265 o del motor schema-driven.

El document no argumenta per quin mecanisme causal "H.265 és complex" implica "cal abandonar multi-NVR". La causa real del descoping (arquitectura centralitzada multi-NVR requereix backend diferent + més esforç) no és la mateixa que la causa de la desviació horària (H.265 + schema-driven). Presentar-ho com una única decisió causa-efecte és una inconsistència en la narrativa de gestió.

**Contrast amb Conclusions (8.2):** *"Durant la fase de planificació, davant de la complexitat tècnica acumulada en la integració de H.265 i el motor schema-driven, es va prendre la decisió estratègica de descoping de reduir l'abast a la versió single-NVR autònoma, prioritzant la qualitat i el lliurament dins del termini."*

Aquí la causalitat és la mateixa però la framing és diferent (motivació positiva vs. mesura d'emergència). Les dues descripcions no son del tot coherents entre sí.

---

### G-02 — ALTA | Secció 2.2.6 vs Capítol 5.9
**Milestone reporta HLS al 60%; el capítol d'implementació el presenta complet sense actualitzar el milestone**

La secció 2.2.6 (fita de seguiment, *"a data de [...] maig de 2026"*) indica:
- *"Mòdul de Gravacions (HLS): 60 % (En fase d'optimització del buffer pel còdec H.265)."*

El Capítol 5.9 descriu una implementació completament acabada del mòdul HLS, incloent: servidor de playlists m3u8, segmentació dinàmica `segment.ts`, gestió de discontinuïtats, suport H.265 SPS/PPS/VPS, i el component Timeline interactiu complet.

El Capítol 6.3 (Taula 6.1) valida el rendiment del sistema HLS de forma satisfactòria.

**El problema:** La secció 2.2.6 mai s'ha actualitzat per reflectir la finalització real. Això pot crear confusió en el tribunal sobre l'estat de lliurament del producte. La subsecció hauria d'incloure una nota explícita del tipus *"A data de lliurament final, tots els mòduls es troben al 100%."*

---

### G-03 — ALTA | Taules 2.5 i 2.6
**Total d'hores finals = 540 exactament — estadísticament sospitós**

La Taula 2.5 mostra: Hores Previstes = 540, Hores Reals = 540, Desviació total = **0 hores**.

Les desviacions internes per fase (AV: +25h, GV: +20h, FM: -25h, GF: -5h, AC: -10h, MC: -5h) s'anulen perfectament fins al darrer zero. En la gestió de projectes real, assolir exactament 0h de desviació total mentre es produeixen desviacions internes significatives (+45h en fases tècniques, -45h en documentació i gestió) és extremadament improbable sense un ajust explícit del registre horari.

La Taula 2.6 (costs de personal) mostra una desviació de +€101.25 (no zero), la qual cosa és consistent amb les hores redist. Però per a un tribunal, el fet que el total sigui exactament 540 en hores però no en diners pot generar preguntes sobre com s'han comptabilitzat les hores reals.

**Recomanació:** Afegir una nota metodològica que expliqui explícitament el mecanisme de transferència d'hores entre fases (reallotjament de les hores documentació estalviades cap a les fases de codi).

---

### G-04 — MITJANA | Seccions 1.2, 2.3.4 i 8.2
**Multi-NVR: objectiu original, pivot estratègic i treball futur alhora — narrativa inconsistent**

**Secció 1.2** (Identificació del problema): *"Lanaccess ha identificat una necessitat de mercat addicional: oferir una versió simplificada i autònoma del programari de gestió, orientada a empreses petites."* — Aquí multi-NVR és la solució principal i single-NVR la versió addicional.

**Secció 1.5** (Solució proposada): *"Desplegament autònom (single-NVR): L'aplicació s'executa directament dins del propi videogravador."* — Aquí single-NVR ja és el cas d'ús principal.

**Secció 2.3.4**: El multi-NVR s'abandona per raons de temps.

**Secció 8.2** (Treball futur): *"Multi-dispositiu centralitzat (Multi-NVR): La gestió centralitzada de múltiples gravadors [...] va formar part de l'abast inicial del projecte."*

El lector no pot determinar si el multi-NVR era: (a) el producte original que es va simplificar, (b) una extensió planificada que es va descoper, o (c) simplement una visió futura. Les tres seccions donen tres respostes incompatibles.

---

### G-05 — BAIXA | Secció 6.5
**UAT amb 8 participants: limitació no reconeguda**

El SUS amb 8 participants és estadísticament limitat. Per a scores quantitatius, Sauro & Lewis (2012) recomanen mínim 12-15 participants per obtenir intervals de confiança raonables. El document no admet aquesta limitació ni calcula l'interval de confiança de la puntuació 85.6.

---

## LENT 3: RIGOR ACADÈMIC I ESTIL

### R-01 — ALTA | Secció 6.3.1
**Reducció 35% CPU: claim quantitatiu sense baseline definit**

El document afirma: *"l'ús de la CPU en el fil d'execució del navegador s'ha reduït en un 35% respecte a prototips previs basats en RxJS/comprovacions estàndard."*

Problemes metodològics:
1. **Baseline inexistent**: Els "prototips previs" no es descriuen en cap punt de la memòria (ni arquitectura, ni codi, ni mesures).
2. **Escenari de test no definit**: Quantes càmeres actives? Quin codec? Quina resolució? Quins components de la UI visibles?
3. **Mètrica ambigua**: "ús de CPU en el fil d'execució del navegador" — mesurat amb el Performance Monitor de DevTools? Amb un profiler de sampling? Durant quant de temps? En quin estat de l'app?
4. **Confusió amb les mesures de memòria**: El paràgraf que descriu les sessions de 2 hores amb Chrome DevTools parla de memòria Heap (Memory tab), no de CPU. El 35% de CPU sembla provenir d'una mesura diferent que no s'explica.

Sense un protocol de mesura reproduïble, aquest claim no és verificable per un tribunal ni per un revisor extern.

---

### R-02 — ALTA | Secció 6.5
**SUS "adaptat" invalida els benchmarks citats**

El document: *"S'ha aplicat el qüestionari System Usability Scale (SUS) adaptat a l'entorn de videovigilància industrial"* i *"es van formular preguntes específiques com: «Consideres que la navegació per trobar paràmetres de xarxa és més intuïtiva ara?»"*

El SUS estàndard (Brooke, 1996) té **10 preguntes específiques i invariables** amb un format de resposta Likert 5 punts estrictament definit. Les "preguntes específiques" mencionades al document **no formen part del SUS estàndard**.

Si l'escala ha estat "adaptada", el score 85.6 no és comparable amb el benchmark de Bangor et al. (2008) que classifica ≥85 com a "Excellent". Citar aquest benchmark per a una escala adaptada és metodològicament incorrecte i pot invalidar la comparació amb sistemes de mercat.

El document hauria de separar clarament: (a) els 10 ítems SUS estàndard (si s'han utilitzat), i (b) les preguntes addicionals d'entrevista específiques del domini, sense barrejar-les.

---

### R-03 — MITJANA | Secció 6.4
**Referència trencada a "Lògica 5.6"**

El text de la Secció 6.4 diu: *"s'ha validat l'eficiència de l'algorisme de backoff exponencial implementat amb RxJS (Lògica 5.6)."*

Al document **no existeix cap element anomenat "Lògica 5.6"**. El Listing 5.6 és el codi de l'authInterceptor (`HttpInterceptorFn`), que no té res a veure amb backoff exponencial. L'algorisme de reconnexió WebSocket mai es mostra en cap listing del document. Aquesta referència trencada indica que hi havia un listing planificat que es va eliminar sense netejar les cross-references.

---

### R-04 — MITJANA | Secció 6.2
**Cobertura de codi >85% sense context**

*"S'ha assolit una cobertura de codi (code coverage) global superior al 85% en els fitxers de serveis."*

Manca:
- Tipus de cobertura (statement? branch? function? line?)
- Nombre total de tests escrits
- Quins fitxers de serveis (tots? Quins s'exclouen?)
- Eina de mesura (Karma + Istanbul? Jest?)
- Si s'inclouen o no els components al còmput (normalment el challenge és cobrir components, no serveis)

Sense aquestes dades, el 85% és un número sense context comparable.

---

### R-05 — MITJANA | Secció 5.9.1
**Contribució a FS2 ambigua acadèmicament**

La Secció 5.9.1 descriu el sistema FS2 amb detall tècnic considerable, incloent la funció `fs2_file_read_buffer`, `aligned_alloc(HD_BYTES_PER_SECTOR, ...)`, i el `Buffer8MBPlayer`. La implementació de la segmentació HLS (seccions 5.9.2 i 5.9.3) és descrita com a desenvolupament propi del projecte.

Però FS2 és descrit com un "sistema d'emmagatzematge propietari de Lanaccess." **Mai s'especifica clarament quina part d'FS2 és preexistent de Lanaccess i quina part és contribució del projecte**. Per a un tribunal, la delimitació de la contribució pròpia vs. infraestructura preexistent és crítica per avaluar el treball del projectista.

---

### R-06 — MITJANA | Listing 4.1
**`unsafe-inline` en CSP presentat sense advertència de seguretat**

La configuració NGINX al Listing 4.1 inclou:
```
Content-Security-Policy: "... style-src 'self' 'unsafe-inline'; ..."
```

El document presenta aquesta configuració com a exemple de "Seguretat Defensiva contra vectors d'atac OWASP" (secció 4.1.2). Però `'unsafe-inline'` en `style-src` és explícitament **desaconsellat per OWASP** i per la CSP Level 2/3 specification perquè permet style injection en certs vectors d'atac (CSS injection, clickjacking via style manipulation).

El document no admet ni justifica aquesta concessió. Una alternativa acceptable seria citar que Angular Emulated Encapsulation genera classes aleatòries (com a nonce equivalent) o usar `style-src-elem` per diferenciar inline styles de stylesheets.

---

### R-07 — MITJANA | Seccions 3.3.1 i 6.3
**Latència màxima mesurada toca el límit del requisit — falta el p95/p99**

Requisit US-01: *"La latència punta a punta (glass-to-glass) ha de ser inferior a 300ms."*
Taula 6.1: *"WebRTC (Directe): 150 – 300ms."*

El valor màxim mesurat (300ms) coincideix exactament amb el límit del requisit. En enginyeria de sistemes, **assolir el límit superior no és equivalent a "complir el requisit"** — implica que en condicions lleugerament pitjors (congestió de xarxa, jitter) el sistema podria superar el límit.

El document no reporta percentils (p95, p99) ni les condicions de xarxa en les que es va obtenir el màxim de 300ms. Per a un sistema de seguretat industrial, un tribunal exigiria conèixer quin percentil de les connexions satisfà el requisit.

---

### R-08 — MITJANA | Secció 5.8.3
**"Patró Strategy d'Angular" — atribució incorrecta**

*"Per estructurar el reproductor multifluix, s'ha aplicat el patró de disseny de programari Strategy d'Angular."*

El patró Strategy és un patró de disseny de la Gang of Four (Gamma et al., 1994), completament independent del framework Angular. La frase suggereix que és un patró propi d'Angular, cosa que és incorrecta. La formulació correcta hauria de ser: *"el patró de disseny Strategy (GoF), implementat amb els mecanismes d'injecció de dependències d'Angular."*

---

### R-09 — BAIXA | Secció 1.4.1
**Anglicismes no glossats en text acadèmic en català**

El document usa *"batteries-included"*, *"time-to-market"*, *"drag and drop"*, *"air-gapped"* i d'altres sense glossa ni cursiva consistent. Mentre que la presència d'anglicismes tècnics és acceptable i habitual en enginyeria, l'ús inconsistent (a vegades cursiva, a vegades sense) és un problema d'estil acadèmic.

---

### R-10 — BAIXA | Taula 1.1
**Taula comparativa — fila "ActiveX+RTSP" sense citar mesures empíriques**

La Taula 6.1 afirma: *"ActiveX+RTSP (Legacy): 800 – 2.000 ms / Baixa (Plugin inestable)"*, font: *"Elaboració pròpia mitjançant proves en entorn de laboratori."*

Però la plataforma ActiveX "dels anys 90" és descrita al document com a **operativament obsoleta i incompatible amb navegadors moderns**. Com s'han mesurat 800-2000ms en una plataforma que no funciona en navegadors actuals? No s'especifica en quin entorn (Internet Explorer? Màquina virtual?) ni quan s'han realitzat aquestes mesures. Sense aquest detall, la comparació de la Taula 6.1 no és verificable.

---

### R-11 — BAIXA | Ús global
**"Descodificació" i "descompressió" usats com a sinònims**

El document usa indistintament "descodificació" (decoding) i "descompressió" (decompression). Tècnicament:
- **Decompression** (descompressió): operació matemàtica d'invertir la compressió espacial/temporal del còdec.
- **Decoding** (descodificació): procés complet d'interpretar el flux de bits (inclou parsing, decompressió, i reconstrucció del frame).

En el context de H.265/H.264, el terme precís és "descodificació" (decoding). Usar "descompressió" com a sinònim és acceptable col·loquialment però imprecís en un document acadèmic d'enginyeria de sistemes.

---

### R-12 — BAIXA | Figura 2.1 i Capítol 4
**GStreamer absent del mapa tecnològic i del capítol d'arquitectura**

La Figura 2.1 (mapa mental del stack tecnològic) mostra: TypeScript, Angular 19, Signals, RTSP/Streaming, HLS, HTTP/REST API, WebRTC, Git, Angular CLI, NPM, i d'altres. **GStreamer no apareix**.

El Capítol 4 (Arquitectura i Disseny) descriu la capa de comunicació i el Frontend però no menciona GStreamer com a component de la infraestructura de vídeo. GStreamer apareix per primera vegada a la Secció 5.8, introduït de forma abrupta com a *"el framework multimèdia industrial GStreamer"*.

Per a un component fonamental en el pipeline de captura de vídeo, la seva absència als capítols de planificació i arquitectura és una omissió notable.

---

## RESUM EXECUTIU PER AL TRIBUNAL

### Punts forts de la memòria
- Descripció técnica molt detallada de temes complexos (VFS SQLite, trans-muxing HLS, negociació SDP)
- Diagrames de seqüència i d'activitat ben construïts i consistents amb el text
- Aplicació coherent de patrons de disseny (Strategy, Observer via Signals, Pessimistic Locking)
- Disseny de seguretat generalment sòlid (HttpOnly, Same-Origin, OWASP) amb l'excepció del query param del lock token

### Àrees de risc per a la defensa
1. **El tribunal pot atacar la causalitat del pivot single-NVR** (G-01): cal preparar una argumentació clara que separi "H.265 és difícil" de "multi-NVR requereix more backend scope"
2. **La prova del 35% CPU reduction** (R-01) és la prova quantitativa més feble del document; cal tenir els DevTools screenshots preparats amb metodologia detallada
3. **El claim de "Zero-persistence/F5 closes session"** (A-09) pot ser qüestionat per un tribunal de seguretat; cal clarificar que es refereix a l'estat Angular, no a la cookie de sessió del servidor
4. **La referència trencada a "Lògica 5.6"** (R-03) és visible i hauria de corregir-se en una revisió final del PDF
5. **La complecte de Zone.js** (A-02) pot ser qüestionada si el tribunal revisa el Listing 5.11; cal preparar la distinció entre "zoneless mode" i "runOutsideAngular"

### Prioritats de correcció (si hi ha revisió possible)
| Prioritat | Acció |
|-----------|-------|
| 1 (immediata) | Corregir "Lògica 5.6" → nom correcte o eliminar referència (R-03) |
| 2 | Afegir nota a 2.2.6 indicant estat final 100% de tots els mòduls (G-02) |
| 3 | Canviar "eliminació de Zone.js" per "reducció de les comprovacions de Zone.js" (A-02) |
| 4 | Clarificar que la "zero-persistence" es refereix a l'estat Angular, no a la cookie server-side (A-09) |
| 5 | Afegir una fila a la Taula 4.1 amb la comunicació interna D-Bus/gRPC/Unix Socket (A-06) |

---

*Auditoria realitzada per Claude Sonnet 4.6 en rol de tribunal avaluador i arquitecte sènior. Basat exclusivament en l'anàlisi estàtica del document `main.pdf`. Cap fitxer del projecte ha estat modificat.*
