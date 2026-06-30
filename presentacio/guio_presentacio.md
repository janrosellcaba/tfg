# Guió Minutat — Defensa TFG: Web Core View
**Jan Rosell Caba · Juny 2026 · Durada total: 25 minuts (20 min presentació + 5 min demo)**

> **Com usar aquest guió**: No llegeixis paraula per paraula. Cada bloc té 3-4 *speaking points* que representen les idees que has de transmetre. Les **paraules en negreta** s'han d'emfatitzar vocalment. La línia de Transició et dóna la frase d'unió per a la diapositiva següent.

---

## RESUM DE TEMPS

| Bloc | Slides | Rang | Durada |
|------|--------|------|--------|
| 1 · Context i Problema | 1–4 | 0:00–3:00 | 3 min |
| 2 · Especificació i Disseny | 5–7 | 3:00–5:15 | 2 min 15s |
| 3 · Implementació | 8–22 | 5:15–13:00 | 7 min 45s |
| 4 · Validació | 23 | 13:00–15:00 | 2 min |
| 5 · Demo en Directe | 24 | 15:00–20:00 | 5 min |
| 6 · Aspectes Transversals | 25–27 | 20:00–22:30 | 2 min 30s |
| 7 · Conclusions i Tancament | 28–29 | 22:30–25:00 | 2 min 30s |
| **TOTAL** | **29 slides** | | **25 min** |

---

## BLOC 1: CONTEXT I PROBLEMA
### Temps total: 3 minuts · Slides 1–4

---

### [0:00 — 30s] Slide 1: Portada

**Objectiu d'impacte:** Captar l'atenció del tribunal i establir el nom del projecte com una solució, no una tecnologia.

**Speaking points:**
- "**Web Core View** és el projecte que elimina el darrer obstacle que impedia a Lanaccess competir en el mercat web modern."
- Un projecte real, en producció, per a una empresa real de videovigilància professional.
- 20 minuts de presentació + 5 de demostració en directe.

**Transició:** "Per entendre per què era necessari, cal conèixer primer el producte i el context."

---

### [0:30 — 30s] Slide 2: Context

**Objectiu d'impacte:** Fixar la restricció de disseny més important: tot ha de córrer **sense infraestructura centralitzada**, dins del propi gravador.

**Speaking points:**
- **Lanaccess Telecom** fabrica gravadors NVR professionals — la família **NXT** — per a videovigilància IP d'infraestructura crítica.
- La restricció clau: la CPU és **compartida** entre descodificació de vídeo, escriptura a disc i servei de la web. Cada cicle compta.
- La paraula clau és **standalone**: el gravador és l'únic servidor disponible. Zero núvol, zero servidors externs.

**Transició:** "Fins al 2022, per accedir a aquest dispositiu calia un plugin d'una tecnologia que Microsoft va tancar definitivament."

---

### [1:00 — 1:00] Slide 3: Problema

**Objectiu d'impacte:** Convèncer que no es tracta d'un *refactor* cosmètic, sinó d'una **necessitat de supervivència competitiva**.

**Speaking points:**
- La plataforma legacy era **ActiveX**: tecnologia dels anys 90, exclusiva d'Internet Explorer 11.
- Microsoft va tancar IE el **juny del 2022**. En aquell moment, cap Mac, cap Linux, cap Chrome podia accedir als gravadors NXT.
- Era una arquitectura **rígida**: qualsevol canvi de firmware requeria modificar el frontend manualment. Un fre directe al *time-to-market*.
- La competència ja oferia interfícies web natives. Els clients ho demanaven.

**Transició:** "L'objectiu que se'ns va plantejar era clar: un reemplaçament complet, modern i autònom."

---

### [2:00 — 1:00] Slide 4: Objectiu

**Objectiu d'impacte:** Deixar clar que l'NVR ha de ser **autosuficient**: serveix l'aplicació, el vídeo i les alarmes, tot sol.

**Speaking points:**
- Una **SPA Angular 19** que es descarrega des del propi gravador i s'executa al navegador del client. Zero instal·lació de client.
- Tres restriccions no negociables: **100% web** (zero plugins), **autònom** (zero núvol) i **escalable** (nous firmwares sense canvis al frontend).
- El tercer punt és la innovació diferencial: el **motor Schema-Driven**, del qual parlarem a la implementació.
- El diagrama que veieu representa tots els protocols sobre un sol port: **HTTPS, WSS, WebRTC i HLS** — tot per **443**.

**Transició:** "Primer, però, havíem d'especificar exactament el que necessitàvem. Vegem els actors i els requisits."

---

## BLOC 2: ESPECIFICACIÓ I DISSENY
### Temps total: 2 min 15s · Slides 5–7

---

### [3:00 — 30s] Slide 5: Stakeholders

**Objectiu d'impacte:** Mostrar que el sistema té **actors humans i no-humans**, i que el tribunal FIB és explícitament un stakeholder.

**Speaking points:**
- Cinc actors: **Operador** (vigilància dia a dia), **Administrador/Tècnic** (configuració i manteniment), **Lanaccess** (costos i competitivitat), **Director/Ponent FIB** (rigor metodològic) i l'**Estudiant** (arquitectura robusta).
- Sis casos d'ús cobrits: Autenticació · Vídeo directe · Gravacions · Alarmes · Configuració dinàmica · Administració.

**Transició:** "D'aquests actors vam derivar els requisits funcionals que condicionen cada decisió d'implementació."

---

### [3:30 — 45s] Slide 6: Requisits

**Objectiu d'impacte:** Els requisits no eren aspiracionals: eren **restriccions dures** que van forçar decisions concretes d'arquitectura.

**Speaking points:**
- L'**Administrador** necessita gestionar el sistema en remot: configuració, firmware, llicències i logs — sense desplaçaments físics al client.
- L'**Operador** necessita veure fins a **4 càmeres en directe simultànies**, accedir a gravacions i gestionar alarmes — des de qualsevol navegador.
- Dos actors, dues lògiques d'ús completament diferents, un sol sistema que les serveix des del hardware del gravador.

**Transició:** "Per satisfer tots dos, vam dissenyar una arquitectura de microserveis especialitzats."

---

### [4:15 — 1:00] Slide 7: Arquitectura

**Objectiu d'impacte:** L'arquitectura no és una llista de tecnologies; és una **resposta deliberada a les restriccions de recursos** del dispositiu.

**Speaking points:**
- La **SPA Angular** s'executa al navegador del client: allibera la CPU del gravador per a vídeo i disc.
- Al backend, **microserveis C++ especialitzats**: `la-core-legacy` (Config API + Auth), `la-db-engine` (SQLite via gRPC), `la-core-alarm` i `la-core-notifier` (push via WSS), `la-video-engine` + **MediaMTX** (WebRTC/WHEP i HLS).
- **NGINX** com a únic punt d'entrada al port **443**: imposa regles de seguretat a tota la plataforma, gestiona les cookies **HttpOnly + SameSite=Strict** — el JavaScript mai pot llegir la sessió. Elimina d'arrel **OWASP A07**.
- Si un microservei cau, la resta segueixen funcionant: aïllament per disseny.

**Transició:** "Amb l'arquitectura definida, passem al cor: la implementació de les quatre pàgines principals."

---

## BLOC 3: IMPLEMENTACIÓ
### Temps total: 7 min 45s · Slides 8–22

> **Estratègia per a aquest bloc**: Els slides de captura de pantalla (1/N de cada secció) s'han de passar en 15–20 s mostrant la interfície. El temps real és als slides de detall tècnic.

---

### [5:15 — 20s] Slide 8: Sistema de Configuració 1/3 *(screenshot)*

**Objectiu d'impacte:** Mostrar la interfície real abans d'explicar el mecanisme darrere.

**Speaking points:**
- El que veieu és la pàgina de configuració del gravador: xarxa, vídeo, comptes d'usuari...
- Cada camp, cada validació, cada `<select>` — **cap enginyer de frontend l'ha escrit a mà**.
- El formulari el genera Angular automàticament. Vegem com.

**Transició:** "La clau és el JSON Schema que el backend envia."

---

### [5:35 — 40s] Slide 9: Sistema de Configuració 2/3 *(JSON Schema)*

**Objectiu d'impacte:** El backend no envia dades, envia **metadades**. És la innovació arquitectònica clau.

**Speaking points:**
- Cada paràmetre de configuració porta `_type` (ipaddr, boolean, list, numeric, password...), `_default`, `_allowedValues`, `_minValue`, `_maxValue`.
- Angular llegeix l'esquema i renderitza el formulari complet de forma automàtica. Exemple: `_type: "ipaddr"` → input amb **regex de validació IP**. `_allowedValues: ["VIVOTEK","ONVIF","RTSP"]` → **`<select>`** automàtic.
- Impacte: **nou model NVR amb 20 paràmetres nous** → zero canvis al frontend. Zero PR, zero deploy, zero cost.

**Transició:** "Ara bé, si dos tècnics editen la mateixa IP simultàniament sense control, tenim un problema de concurrència."

---

### [6:15 — 50s] Slide 10: Sistema de Configuració 3/3 *(Config Lock)*

**Objectiu d'impacte:** El *pessimistic lock* és la solució a un problema real de xarxa: dos tècnics editant la IP del gravador alhora pot deixar-lo inaccessible.

**Speaking points:**
- Quan el tècnic entra a configurar, Angular fa **POST `/lock`**: el backend retorna un **token de 32 caràcters hexadecimals**. Tota escriptura posterior ha d'incloure'l — injectat automàticament per l'**HTTP Interceptor**.
- El token es **renova cada `expiresIn/2` segons** (heartbeat automàtic). Si el tècnic tanca el portàtil, el lock **expira sol** en 60–120 s. Cap sistema bloquejat per sempre.
- Qualsevol altre usuari que intenti editar rep **HTTP 423 Locked** → entra en **mode lectura automàticament**, sense cap missatge d'error confús.
- El diagrama de seqüència de la dreta mostra el flux complet d'adquisició, renovació i alliberament.

**Transició:** "Configuració resolta. La pàgina més crítica és el vídeo en directe, on la latència és la mètrica que ho decideix tot."

---

### [7:05 — 15s] Slide 11: Vídeo en Directe 1/4 *(screenshot)*

**Objectiu d'impacte:** Mostrar la interfície: quatre càmeres en mosaic, drag-and-drop, sensors en temps real.

**Speaking points:**
- Fins a **4 càmeres simultànies** en mosaic. Drag-and-drop per reordenar. Sensors i relés en temps real al panell lateral.
- Tot des de qualsevol navegador, sense cap plugin. Vegem el mecanisme.

**Transició:** "El diagrama d'activitat explica com el sistema tria WebRTC o HLS."

---

### [7:20 — 40s] Slide 12: Vídeo en Directe 2/4 *(diagrama d'activitat)*

**Objectiu d'impacte:** El protocol s'escull automàticament; l'operador mai ho percep.

**Speaking points:**
- Angular intenta primer **WebRTC/WHEP** (RFC 9716): un sol `POST` al servidor per negociar. Transport **SRTP/UDP + DTLS** → **150–300 ms** de latència. L'antiga plataforma: **800–2000 ms**. Els rangs **no se solapen**.
- Si UDP és bloquejat per un tallafocs corporatiu → **fallback automàtic a HLS en ≤ 5 s**, transparent per l'operador. TCP/HTTPS passa qualsevol restricció.
- Fallada de connexió → **reintent cada 2 s**. Límit superat → "**NO SIGNAL**". Quan l'operador tanca la finestra, els recursos s'alliberen.

**Transició:** "El component de vídeo pot usar WebRTC o HLS intercanviablement gràcies al patró Strategy."

---

### [8:00 — 40s] Slide 13: Vídeo en Directe 3/4 *(Strategy pattern)*

**Objectiu d'impacte:** Una interfície TypeScript és el que fa que afegir un protocol nou no toqui cap component.

**Speaking points:**
- La interfície `StreamingStrategy` defineix quatre contractes: `connectionState` (Signal), `errorMessage` (Signal), `startStream()` i `closeStream()`.
- El component de vídeo crida sempre `startStream()` de la **mateixa manera**, sense saber quin servei hi ha darrere.
- Angular injecta `WebRTCService` o `HlsService` en funció del protocol actiu: **zero canvis al component**.
- Si demà afegim un protocol nou: s'implementa la interfície i s'injecta. La resta segueix igual.

**Transició:** "El quart slide de vídeo cobreix el panell I/O: sensors i relés en temps real."

---

### [8:40 — 30s] Slide 14: Vídeo en Directe 4/4 *(IO_NOTIFY WebSocket)*

**Objectiu d'impacte:** Quan un sensor físic canvia d'estat, la interfície s'actualitza sense que l'operador faci res.

**Speaking points:**
- En obrir el panell I/O, Angular fa una subscripció via WebSocket: **`filter_update`** amb els topics `device/notification` i `io`.
- Quan un sensor o relé canvia al maquinari: **D-Bus** → **`la-core-notifier`** → JSON **`IO_NOTIFY`** → WebSocket → Angular **Signal** → s'actualitza **únicament l'element afectat** del DOM.
- Cap polling, cap renderitzat global: eficiència màxima en una CPU compartida.

**Transició:** "Vídeo i configuració resolts. Les gravacions presenten un repte diferent: servir H.265 a un navegador estàndard."

---

### [9:10 — 15s] Slide 15: Gravacions 1/3 *(screenshot)*

**Objectiu d'impacte:** Mostrar la línia de temps interactiva.

**Speaking points:**
- La línia de temps interactiva permet navegar per qualsevol moment, qualsevol càmera, qualsevol stream.
- Fins a **x8** de velocitat de reproducció. La interfície sembla simple; el que hi ha darrere és complex.

**Transició:** "Vegem com es construeix la playlist HLS a partir del sistema de fitxers propietari."

---

### [9:25 — 50s] Slide 16: Gravacions 2/3 *(pipeline HLS + transmuxing)*

**Objectiu d'impacte:** **Trans-muxing ≠ transcoding**: canviem l'embolcall, no el bitstream. CPU quasi zero al gravador.

**Speaking points:**
- Angular demana `/hls-play.m3u8?token=...&start=...&end=...`. El microservei `la-video-engine` busca els blocs **FS2** del rang temporal i composa la playlist dinàmicament.
- Si hi ha un tall de gravació de més de **3 s** → `#EXT-X-DISCONTINUITY` a la playlist: el descodificador no es congela.
- Per cada segment sol·licitat: llegir bloc **FS2** del disc (buffer 8 MB), recuperar capçaleres **SPS/PPS/VPS** del keyframe anterior si cal, empaquetar com **MPEG-TS**, servir `segment.ts`.
- **H.265 (HEVC)**: **50% menys disc** que H.264 per la mateixa qualitat. Factor crític en un gravador que funciona 24/7.

**Transició:** "El slide següent mostra una playlist real generada per un desplegament a Mèxic."

---

### [10:15 — 25s] Slide 17: Gravacions 3/3 *(M3U8 real)*

**Objectiu d'impacte:** Connectar la teoria amb un artefacte real generat en producció.

**Speaking points:**
- Cada `segment.ts` porta: ID del bloc FS2, offset de buffer, rang temporal ISO 8601, i el token d'autenticació. El navegador descarrega els segments sota demanda.
- `#EXT-X-TARGETDURATION: 57` — cap segment supera 57 s, garantia de l'especificació HLS.
- Aquesta playlist la va generar un NVR NXT en un client real a **Mèxic**. No és un prototip.

**Transició:** "La quarta pàgina és alarmes, on la fiabilitat és el requisit absolut: cap alerta es pot perdre."

---

### [10:40 — 15s] Slide 18: Alarmes 1/4 *(screenshot)*

**Objectiu d'impacte:** Mostrar la interfície d'alarmes.

**Speaking points:**
- Notificacions en temps real, historial filtrable, gestió d'alarmes actives. La icona canvia sense que l'operador faci res.

**Transició:** "El mecanisme darrere té tres threads independents per garantir que cap alarma es perdi."

---

### [10:55 — 40s] Slide 19: Alarmes 2/4 *(pipeline 3 threads)*

**Objectiu d'impacte:** Els tres threads estan **desacoblats**: si un es bloqueja, els altres no s'aturen.

**Speaking points:**
- `la-core-alarm` té un **pipeline de 3 threads** independents:
  1. **D-Bus**: escolta `ALARM_NOTIFY` → cua *thread-safe*.
  2. **Persistència**: escriu via **gRPC** a `la-db-engine` — garantia ACID.
  3. **Emissió**: difon JSON a tots els clients via **WSS** — push instantani.
- Clau: si gRPC es bloqueja (disc lent), el WebSocket **no s'atura**. L'operador segueix rebent notificacions.
- El frontend **mai fa polling**: tot és *push*. Zero overhead en una CPU compartida.

**Transició:** "La persistència d'alarmes usa una solució no trivial per garantir atomicitat en hardware embegut."

---

### [11:35 — 35s] Slide 20: Alarmes 3/4 *(persistència SQLite VFS)*

**Objectiu d'impacte:** El driver VFS personalitzat és la peça que garanteix **zero corrupció en talls de corrent**.

**Speaking points:**
- `la-core-alarm` escriu via **gRPC** a `la-db-engine`, que usa **SQLite3** amb un driver VFS personalitzat: `remote_vfs`.
- `remote_vfs` intercepta **tot l'I/O** de SQLite i el redirigeix per **Unix Domain Socket** al `la-video-engine`, que és l'únic procés amb accés directe al disc.
- Mode **WAL + `synchronous=NORMAL`**: propietats ACID completes. SQL Trigger de poda automàtica a **10.000 registres** màxim.

**Transició:** "El slide final d'alarmes explica per qué tota l'escriptura passa per la-video-engine."

---

### [12:10 — 25s] Slide 21: Alarmes 4/4 *(UDS + FS2)*

**Objectiu d'impacte:** **Un disc, dos serveis**: la cohesió és una restricció de hardware, no una decisió de disseny opcional.

**Speaking points:**
- `remote_vfs` envia operacions VFS binàries (xRead, xWrite, xSync, xTruncate) per **Unix Domain Socket**.
- Escriptures **alineades a 512 B**: atomicitat garantida per sector. Cap corrupció en tall elèctric.
- El disc és compartit: **SQLite3** (alarmes) + **FS2** (vídeo propietari Lanaccess). `la-video-engine` és l'**únic escriptor** — evita col·lisions d'I/O a nivell de firmware.

**Transició:** "A més de les quatre pàgines principals, hi ha un conjunt de mòduls que completen el sistema."

---

### [12:35 — 25s] Slide 22: Altres Mòduls Implementats

**Objectiu d'impacte:** El projecte és un sistema complet, no quatre pàgines aïllades.

**Speaking points:**
- **Autenticació i Core**: Login amb Keep-Alive, alertes en temps real, panell NVR i gestió d'usuari.
- **Manteniment**: Actualització de firmware, Backup/Restore/Factory Reset, Registre d'Esdeveniments amb descàrrega filtrada, Ajuda integrada.
- **UX**: Tauler principal, disseny responsiu en SCSS modular, internacionalització en tres idiomes (EN · ES · FR), gestió de llicències.
- **Infraestructura**: NGINX, Assistents de configuració (Wizards), Eina de xarxa amb Web Proxy tunnels.

**Transició:** "Passem ara a les dades que validen que tot plegat funciona en condicions reals."

---

## BLOC 4: VALIDACIÓ
### Temps total: 2 minuts · Slide 23

---

### [13:00 — 2:00] Slide 23: Validació

**Objectiu d'impacte:** Les proves no van ser en un laboratori: van ser en producció real, amb instal·ladors reals, a **Mèxic**.

**Speaking points:**
- **Cicle de desplegament**: `ng build` + `scp` → NVR en xarxa local. Firmware update → serveis backend. Un cicle complet en minuts.
- **Proves en camp real**: NVR desplegats a **Mèxic**. Feedback directe d'instal·ladors → millores iteratives incorporades.
- **Ús real des del dia 1**: cap entorn de *staging*, cap usuari fictici. La validació és producció.
- Resultats clau validats: **WebRTC ≤ 300 ms** en camp · **CPU −35%** respecte a ActiveX · **Zero regressió crítica** en producció.

**Transició:** "Passem ara a la demostració, on veureu tot això en funcionament real sobre un NVR NXT."

---

## BLOC 5: DEMOSTRACIÓ EN DIRECTE
### Temps total: 5 minuts · Slide 24

---

### [15:00 — 5:00] Slide 24: Demo

**Objectiu d'impacte:** Fer visible i tangible el sistema — **veure** la latència, **veure** el formulari Schema-Driven, **veure** les alarmes push — en viu.

> **Preparació obligatòria** (fer ABANS d'iniciar la presentació):
> - Navegador obert a `https://<ip-nvr>/` en una finestra separada, **minimitzada**.
> - Confirmar que el NVR és accessible des de la xarxa de la sala.
> - Segon navegador en **pestanya incògnit** llest per simular un segon operador.
> - Carpeta amb **screenshots** de cada pas com a pla B si cau la connexió.

**Seqüència (5 minuts en directe):**

1. **(15:00–15:45) — Login**: Alt+Tab, obrir el navegador. "La URL que veieu és la IP directa del gravador: **zero servidors externs**, zero núvol." Iniciar sessió. Si el tribunal pregunta per les cookies: F12 → Application → Cookies → mostrar `HttpOnly = true`.

2. **(15:45–17:00) — Mosaic WebRTC**: Obrir 4 càmeres en mosaic. "**Fixeu-vos en la latència**: moure un objecte davant de la càmera. La imatge respon en menys de 300 ms. L'antiga plataforma ActiveX tardava entre 800 ms i 2 segons."

3. **(17:00–18:00) — Timeline HLS / Gravacions**: Navegar a la pantalla de gravacions. "Aquí la **línia de temps interactiva**. Busco un moment d'ahir..." Fer un *seek* i reproduir uns segons. "Els segments `.ts` els serveix `la-video-engine` directament des del disc del gravador, sense transcoding."

4. **(18:00–19:00) — Alarma push**: Activar un sensor físic o relay manual. "Veieu que **sense que l'operador faci res**, la icona canvia en temps real." Passar al segon navegador (pestanya incògnit). "Si hi ha un segon operador connectat simultàniament, **tots dos ho veuen alhora**."

5. **(19:00–20:00) — Motor Schema-Driven**: Entrar a Configuració → Xarxa. "El formulari que veieu — IP, màscara, gateway, DNS — **no l'ha escrit cap enginyer de frontend**. Angular l'ha generat automàticament a partir del JSON Schema que acaba d'enviar el firmware. Si demà surt un nou model de NVR amb 20 paràmetres nous, el frontend els dibuixarà sols. Zero canvis."

**Transició:** "Tanquem la demo. Dediquem els últims minuts als aspectes transversals i les conclusions."

---

## BLOC 6: ASPECTES TRANSVERSALS
### Temps total: 2 min 30s · Slides 25–27

---

### [20:00 — 1:00] Slide 25: Planificació

**Objectiu d'impacte:** Les desviacions van recaure **exactament** en les aportacions tècniques més innovadores. Això prova que el risc estava ben localitzat i identificat.

**Speaking points:**
- Total previst: **540 h**. Total executat: **585 h**. Desviació: **+45 h (8,3%)** — dins del **10% de contingència** previst.
- Desviació 1: **H.265 trans-muxing (+25 h)**. No es va estimar prou la complexitat de recuperar capçaleres SPS/PPS/VPS en el FS2 propietari Lanaccess.
- Desviació 2: **Motor Schema-Driven (+20 h)**. El nombre de tipus de camp necessaris — IP, llistes dinàmiques, camps condicionats — va superar l'estimació inicial.
- Cap funcionalitat va ser retallada ni ajornada. La contingència es va usar en un **76%**, no al 100%.

**Transició:** "Aquestes hores extra van tenir un cost, però el pressupost final va romandre per sota del màxim previst."

---

### [21:00 — 45s] Slide 26: Pressupost

**Objectiu d'impacte:** El projecte ha entregat **tot l'abast, a temps, i per sota del pressupost màxim**.

**Speaking points:**
- Total executat: **16.061,50 €** vs 16.420,25 € previstos. **Estalvi net: 358,75 €**.
- Perfil típic de software: **>90% és personal**. El hardware és amortització d'equips ja existents.
- La contingència del 10% va absorbir les 45 hores extra sense exhaurir-se del tot: **76% usada, 24% restant**.

**Transició:** "Més enllà dels números, el projecte té un impacte real en tres dimensions de sostenibilitat."

---

### [21:45 — 45s] Slide 27: Sostenibilitat i Impacte Social

**Objectiu d'impacte:** La sostenibilitat no és un exercici acadèmic: les dades tècniques del projecte **generen impacte mesurable**.

**Speaking points:**
- **Ambiental**: el **35% de reducció de CPU** en terminals que funcionen 24/7 es tradueix en estalvi energètic a escala de flota. La gestió remota elimina desplaçaments de tècnics.
- **Social**: accessibilitat total des de Mac, Linux i Chrome; accés a mercats abans tancats. Interfície clara → menys fatiga en torns de vigilància. Seguretat sempre amb **judici humà**: cap decisió automatitzada sense supervisió.
- **Econòmica**: Schema-driven → nou model NVR a **zero cost de frontend**. *Single-NVR* sense núvol → accessible per a **PIMEs** que no poden pagar infraestructura centralitzada.

**Transició:** "Tanquem repasant si hem complert tot el que ens vam proposar."

---

## BLOC 7: CONCLUSIONS I TANCAMENT
### Temps total: 2 min 30s · Slides 28–29

---

### [22:30 — 2:00] Slide 28: Conclusions

**Objectiu d'impacte:** No és una llista de *checkboxes*: cada ítem representa un **problema real que ara no existeix**.

**Speaking points:**
- **Plataforma 100% web** sense plugins: un client amb Mac o Linux ja pot accedir als NVR NXT. IE ja no és un requisit.
- **Latència vídeo < 300 ms**: el requisit de vigilància activa és complert. Les sales de control no experimenten el retard de l'antiga plataforma.
- **Totes les funcionalitats plantejades** entregades: les quatre pàgines principals i tots els mòduls de manteniment i administració.
- **Seguretat** (OWASP Top 10, HttpOnly + SameSite=Strict, TLS 1.2/1.3, config lock, zero LocalStorage de credencials): validada per disseny, no afegida al final.
- **Reducció de CPU del 35%** i **desplegament autònom**: validats en camp. **Pressupost i terminis**: respectats, amb estalvi net.
- Quatre línies de futur: dashboard de reports en temps real · imatges a les alarmes · anàlisi de vídeo amb IA · app mòbil.

**Transició:** "Amb tot això, tanco la presentació. Gràcies."

---

### [24:30 — 30s] Slide 29: Tancament

**Objectiu d'impacte:** Tancar amb **autoritat i calma**: domines el tema, estàs preparat per a qualsevol pregunta.

**Speaking points:**
- "Moltes gràcies al tribunal per la vostra atenció. **Quedo a disposició per a qualsevol pregunta.**"
- Recordatori ràpid de les tres xifres clau si cal: latència **150–300 ms** WebRTC · **35%** menys CPU · desviació absorbida en el **10% de contingència**.
- Postura: mans sobre la taula, contacte visual amb el tribunal, veu ferma. No afegeixis res més. **El silenci és autoritat.**

---

## PREGUNTES TRAMPA FREQÜENTS — Respostes preparades

---

### "Per què *Pessimistic Lock* i no *Optimistic Lock*?"
> "L'*Optimistic Lock* deixa que dues escriptures coexisteixin i resol el conflicte *a posteriori*. En configuració de xarxa d'un gravador de seguretat, una escriptura concurrent pot deixar la IP en un estat inconsistent i fer l'equip inaccessible per xarxa. El cost d'un *pessimistic lock* — un tècnic en mode lectura mentre l'altre configura — és negligible comparat amb el risc d'un dispositiu inaccessible a infraestructura crítica."

---

### "SQLite en un sistema embegut no és massa pesat?"
> "SQLite és el motor de base de dades més desplegat del món en dispositius embeguts — Android, iOS i Firefox tots el fan servir. El `remote_vfs` és la nostra capa de control que garanteix l'alineació de sectors i l'atomicitat específics del nostre hardware. No estem inventant una base de dades; estem configurant-ne una de provada per al nostre cas d'ús concret."

---

### "Per què no usar JWT en comptes de cookies?"
> "JWT al LocalStorage és trivial de robar per XSS: qualsevol script injectat pot fer `localStorage.getItem('token')`. Una cookie `HttpOnly` és *invisible* per a JavaScript: cap codi pot llegir-la. En un sistema amb accés a càmeres d'infraestructura crítica, no acceptem aquell risc. Les cookies `HttpOnly + Secure + SameSite=Strict` eliminen la superfície d'atac d'OWASP A07 per disseny, no com a parche."

---

### "La reducció del 35% de CPU no és una dada massa genèrica?"
> "No. És una dada mesurada en hardware concret: Intel Core i3-8100T, 4 nuclis a 3,10 GHz, 8 GB RAM, Windows 10 IoT, streaming de 4 càmeres simultànies amb alertes WebSocket actives. Comparat amb un prototip equivalent basat en Zone.js + RxJS en el mateix hardware, la reducció és del 35% en el fil principal del navegador. La comparació és controlada i l'escenari és el cas d'ús real de producció."

---

### "H.265 als navegadors no és un problema de patents?"
> "El problema de patents és real però ortogonal al nostre cas. El *transcoding* H.265 requeriria una llicència HEVC. El *trans-muxing* no recodifica: simplement canvia el contenidor de FS2 propietari a MPEG-TS. El client que descodifica H.265 és el navegador o el sistema operatiu del client final, que ja gestiona les llicències de descodificació per compte propi."

---

### "La latència WebRTC de 150–300 ms és mesurable o estimada?"
> "Mesurable i mesurada en camp: NVR NXT desplegats a Mèxic, xarxes locals reals. WebRTC/WHEP: 150–300 ms. ActiveX+RTSP legacy: 800–2000 ms. Els rangs no se solapen: el pitjor cas de WebRTC (300 ms) és millor que el millor cas d'ActiveX (800 ms). La millora és irrefutable, no una estimació."

---

### "Per què el vfs escriu a la-video-engine i no directament al disc?"
> "Perquè `la-video-engine` és l'únic procés que coneix la geometria exacta del disc: sectors del sistema de fitxers FS2, alineació de blocs, metadata de gravació. Si SQLite escrivís directament, podria corrompre el filesystem de vídeo. Centralitzar tot l'I/O a `la-video-engine` via UDS garanteix que no hi ha col·lisions entre el sistema d'alarmes i el sistema de vídeo, fins i tot sota càrrega de gravació 24/7."

---

### "Per què Angular 19 i no React o Vue?"
> "Decisió pragmàtica: l'equip de Lanaccess ja tenia coneixement intern d'Angular, la DI nativa facilita el patró Strategy per als protocols de vídeo, i els Signals (estabilitzats a Angular 17+) eren la resposta directa al problema de rendiment en CPU compartida. No és una qüestió de framework superior: és la solució més eficient per a les restriccions concretes d'aquest projecte."

---

*Fi del guió — Jan Rosell Caba · Web Core View · Juny 2026*
