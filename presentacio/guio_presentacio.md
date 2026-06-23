# Guió Minutat — Defensa TFG: Web Core View
**Jan Rosell Caba · Juny 2026 · Durada total: 25 minuts**

> **Com usar aquest guió**: No llegeixis paraula per paraula. Cada bloc té 3-4 *speaking points* que representen les idees que has de transmetre. Les **paraules en negreta** s'han d'emfatitzar vocalment. La línia de Transició et dóna la frase d'unió per a la següent diapositiva.

---

## BLOC 1: CONTEXT I PROBLEMA
### Temps total: 3 minuts (Slides 1–4)

---

### [0:00 — 30s] Slide 1: Portada

**Objectiu d'impacte:** Captar l'atenció del tribunal i establir el nom del projecte com una solució, no una tecnologia.

**Speaking points:**
- "**Web Core View** és el projecte que elimina el darrer obstacle que impedia a Lanaccess competir en el mercat web modern."
- És un projecte real, en producció, per a una empresa real de videovigilància professional.
- En 20 minuts + 5 de demostració, veureu el perquè, el com, i els resultats.

**Transició:** "Per entendre la magnitud del problema que resolem, cal conèixer primer el producte i el context."

---

### [0:30 — 30s] Slide 2: Lanaccess i els Gravadors NVR

**Objectiu d'impacte:** Fixar la restricció de disseny més important: tot ha de córrer **sense Internet**, dins d'un dispositiu embegut.

**Speaking points:**
- Lanaccess fabrica gravadors NVR professionals: fins a **152 càmeres** simultànies per unitat.
- El maquinari és un **SoC ARM NXP** amb Linux Yocto: recursos limitats, sense connexió a núvol.
- La paraula clau és **standalone**: l'NVR és l'únic servidor disponible.

**Transició:** "Ara bé, fins al 2022, per accedir a aquest dispositiu calia un plugin d'una tecnologia que Microsoft va tancar."

---

### [1:00 — 1:00] Slide 3: El Problema — La Fi d'una Era

**Objectiu d'impacte:** Convèncer que no es tracta d'un *refactor* cosmètic, sinó d'una **necessitat de supervivència competitiva**.

**Speaking points:**
- La plataforma legacy era **ActiveX**: tecnologia dels anys 90, exclusiva d'Internet Explorer 11.
- Microsoft va tancar IE el **juny del 2022**. En aquell moment, cap Mac, cap Linux, cap Chrome podia accedir als gravadors NXT.
- A més, era una arquitectura rígida: **cada canvi de firmware requeria modificar el frontend** manualment.
- La competència ja oferia interfícies web natives. Els clients ho demanaven.

**Transició:** "L'objectiu que se'ns va plantejar era clar: construir un reemplaçament complet, modern i autònom."

---

### [2:00 — 1:00] Slide 4: L'Objectiu — Un Sistema Web Autònom

**Objectiu d'impacte:** Deixar clar que l'NVR ha de ser **autosuficient**: serveix l'app, el vídeo i les alarmes, tot sol.

**Speaking points:**
- La solució: una **SPA Angular 19** que es descarrega des del propi gravador i s'executa al navegador del client.
- Tres restriccions no negociables: **100% web** (zero plugins), **autònom** (zero núvol), i **escalable** (nous firmwares sense canvis al frontend).
- El tercer punt és la innovació diferencial: el **motor Schema-Driven**, del qual parlarem a la implementació.

**Transició:** "Abans d'implementar, havíem d'especificar exactament el que necessitàvem. Vegem els actors i els requisits crítics."

---

## BLOC 2: ESPECIFICACIÓ I DISSENY
### Temps total: 4 minuts (Slides 5–12)

---

### [3:00 — 20s] Slide 5: Els Actors del Sistema

**Objectiu d'impacte:** Transmetre que el gravador no és només hardware; és un **actor actiu** que dicta les regles.

**Speaking points:**
- Tres actors: **Administrador** (configuració total), **Operador** (vigilància dia a dia), i el propi **Sistema NVR** com a actor actiu.
- El NVR emet alarmes, dicta l'esquema JSON i gestiona els *config locks*: no és passiu.
- Sis casos d'ús derivats: autenticació, vídeo directe, gravacions, alarmes, configuració dinàmica, administració.

**Transició:** "D'aquests actors vam derivar els requisits no funcionals crítics, que van determinar tota l'arquitectura."

---

### [3:20 — 40s] Slide 6: Requisits Crítics — Rendiment i Vídeo

**Objectiu d'impacte:** Els RNF no eren aspiracionals: eren **restriccions de disseny dures** que van forçar decisions concretes.

**Speaking points:**
- **RNF-01**: latència de vídeo **< 300 ms** en P50, < 350 ms en P95. Motiu: en una sala de control activa, 2 segons de retard és inacceptable per a seguretat.
- **RNF-02**: si UDP bloquejat, **fallback automàtic a HLS en ≤ 5 segons** — transparent per l'operador.
- **RNF-03**: zero polling HTTP, actualitzacions granulars. La CPU de l'NVR ARM és limitada.
- **RNF-04**: WebSocket amb **reconexió ≤ 30 s** amb backoff exponencial.

**Transició:** "Igual d'importants eren els requisits de seguretat, que vam imposar-nos com a criteri d'acceptació."

---

### [4:00 — 30s] Slide 7: Requisits Crítics — Seguretat i Privadesa

**Objectiu d'impacte:** La seguretat és una **restricció de disseny**, no una capa afegida al final.

**Speaking points:**
- **Zero LocalStorage** per a credencials: qualsevol XSS al navegador podria robar-les. Vam optar per cookies **HttpOnly + Secure + SameSite=Strict**.
- **TLS 1.2/1.3** amb AESGCM: tot el tràfic xifrat, inclosos els WebSockets (WSS).
- **Privacy by Design**: les dades sensibles només existeixen a RAM. Un F5 buida la sessió completament — zero persistència.
- Compliment **OWASP Top 10** i **RGPD** com a criteri d'acceptació formal.

**Transició:** "Amb els requisits clars, dissenyem l'arquitectura que els satisfà."

---

### [4:30 — 50s] Slide 8: Arquitectura Global

**Objectiu d'impacte:** L'arquitectura no és una llista de tecnologies; és una **resposta deliberada a les restriccions de recursos**.

**Speaking points:**
- Separació en dos mons: la **SPA Angular** s'executa al navegador del client — allibera la CPU del gravador embegut.
- Al backend, **microserveis C++ especialitzats**: cada procés és propietari d'un domini aïllat. Si el motor de vídeo falla, la configuració segueix funcionant.
- **NGINX** com a únic punt d'entrada al port 443: imposa les regles de seguretat a tota la plataforma, oculta els ports interns.

**Transició:** "Vegem quins processos específics corren dins del gravador."

---

### [5:20 — 30s] Slide 9: Els Microserveis al Dispositiu

**Objectiu d'impacte:** Demostrar que hi ha un disseny de responsabilitats pensat, no un monòlit disfressat.

**Speaking points:**
- Capa de comunicació: **`la-core-legacy`** (Config API, Auth), **`la-db-engine`** (SQLite via gRPC), **`la-core-notifier`** i **`la-core-alarm`** (WebSockets push).
- Capa de vídeo: **`la-video-engine`** (captura, disc, HLS) + **MediaMTX** (WebRTC/WHEP).
- Comunicació interna per **Unix Domain Sockets**: el canal IPC més ràpid de Linux, sense passar per NGINX.
- Tots gestionats per **systemd** sobre Yocto Linux.

**Transició:** "NGINX és la capa que unifica tot i que imposa la seguretat."

---

### [5:50 — 30s] Slide 10: NGINX — La Frontera de Seguretat

**Objectiu d'impacte:** NGINX és la raó per la qual l'arquitectura és **segura per disseny**, no per configuració.

**Speaking points:**
- Port **443** com a únic punt d'entrada: `GET /stream/` va a MediaMTX, `GET /ws/` va als notificadors. Cap port intern és visible.
- NGINX gestiona les cookies amb **HttpOnly + SameSite=Strict**: el JavaScript de l'aplicació mai pot llegir la sessió.
- Elimina d'arrel la vulnerabilitat **OWASP A07** (Identification & Authentication Failures). Cap JWT al LocalStorage.

**Transició:** "Hi havia un altre problema de disseny que NGINX no pot resoldre sol: la concurrència en configuració."

---

### [6:20 — 20s] Slide 11: El Problema de Concurrència en Configuració

**Objectiu d'impacte:** El *race condition* és **real** i la seva conseqüència és la corrupció silenciosa de la configuració del dispositiu.

**Speaking points:**
- Dos admins editen simultàniament la IP del gravador: sense control, **s'aplica l'últim que escriu** i els canvis de l'altre s'esfumen.
- En xarxes de seguretat amb múltiples tècnics, aquesta col·lisió no és teòrica; ocorre.
- El resultat: dispositiu en **estat inconsistent**, possiblement inaccessible per xarxa.

**Transició:** "La solució és un mecanisme de *Pessimistic Lock* inspirat en la gestió de transaccions de bases de dades."

---

### [6:40 — 20s] Slide 12: Disseny del Config Lock Pessimista

**Objectiu d'impacte:** El *lock* és **tolerant a fallades** — no pot bloquejar el sistema indefinidament.

**Speaking points:**
- **POST `/lock`** retorna un token de **32 caràcters hex** únic: qualsevol escriptura posterior ha d'incloure'l.
- Expiració automàtica en **60-120 s** sense renovació: si el tècnic tanca el portàtil, el cadenat caduca sol. Cap procés queda bloquejat per sempre.
- Mentre el lock és actiu, HTTP **423 Locked** posa altres usuaris en mode lectura automàticament.
- La renovació del *keep-alive* va **fora del cicle de Zone.js**: no genera cap re-render innecessari.

**Transició:** "Amb l'arquitectura dissenyada, passem al cor del projecte: la implementació."

---

## BLOC 3: IMPLEMENTACIÓ
### Temps total: 6 minuts (Slides 13–19)

---

### [7:00 — 50s] Slide 13: El Motor Schema-Driven

**Objectiu d'impacte:** Aquesta és la **contribució duradora** del projecte per a Lanaccess: el frontend que s'adapta sol.

**Speaking points:**
- El problema estructural de l'antiga plataforma: **cada canvi de firmware requeria un canvi de frontend**. Fre al mercat.
- La solució: el backend no envia dades, envia **metadades**. Cada camp porta `_type`, `_allowedValues`, `_min`, `_max`, `_default`.
- Angular llegeix l'esquema i **renderitza el formulari complet automàticament**: inputs, selects, checkboxes, validació.
- Impacte empresarial directe: un nou model de NVR amb 20 paràmetres nous → **zero canvis al frontend**. Zero PR, zero deploy.

**Transició:** "Vegem un exemple concret de com l'esquema JSON es tradueix a components Angular."

---

### [7:50 — 40s] Slide 14: L'Esquema JSON en Acció

**Objectiu d'impacte:** El mecanisme és **concret i auditable**: no és màgia, és un *parser* de metadades determinista.

**Speaking points:**
- Exemple real: `ip_address` amb `_type: "ipaddr"` → Angular genera un input amb **expressió regular de validació IP**.
- `codec` amb `_type: "list"` i `_allowedValues: ["H264","H265"]` → genera un **`<select>`** amb exactament aquelles opcions.
- `max_cameras` amb `_type: "integer"`, `_min: 1`, `_max: 152` → input numèric amb rang **[1, 152]** ja configurat.
- El motor gestiona **10 tipus de camp** que cobreixen el 100% de la configuració del firmware.

**Transició:** "A més d'un formulari intel·ligent, el sistema necessitava ser eficient en temps real. Aquí és on entren els Signals."

---

### [8:30 — 50s] Slide 15: D'RxJS a Angular Signals

**Objectiu d'impacte:** La decisió de migrar a Signals no era una moda; era una **solució directa a un problema mesurat de CPU**.

**Speaking points:**
- El problema amb **Zone.js**: intercepta *tots* els events del navegador (clics, timers, WebSockets) i llança una revisió completa de l'arbre de components. Amb 152 càmeres i alarmes constants, la CPU no donava l'abast.
- **Angular 19 Signals** implementa reactivitat *fine-grained*: Angular sap exactament quins components depenen de quins valors. Si un relé canvia d'estat → s'actualitza **una icona**, no tot el DOM.
- Resultat mesurat en un terminal **Intel Core i3-8100T** sota Windows 10 IoT: **35% de reducció de CPU** en ús continu.
- Per a un operador amb la pantalla oberta 8 hores al dia en un torn de seguretat, la diferència és substancial.

**Transició:** "L'eficiència al navegador estava resolta. El repte de vídeo era diferent: calia latència < 300 ms."

---

### [9:20 — 50s] Slide 16: Orquestració de Vídeo — WebRTC/WHEP (Directe)

**Objectiu d'impacte:** **WebRTC via WHEP** és la decisió que permet latències perceptualment imperceptibles en una sala de control.

**Speaking points:**
- La restricció: < 300 ms imposa **UDP**. TCP afegeix overhead de retransmissions que l'escenari de vigilància no permet.
- **WHEP** (RFC 9716): estandarditza la negociació *egress* de WebRTC amb una sola crida **HTTP POST** a `/stream/<cam>/whep`. Integra perfectament amb MediaMTX.
- Transport: **SRTP/UDP + DTLS** per al xifrat, **ICE** (RFC 8445) per a la travessia de NATs i tallafocs.
- Resultat: latència **150–300 ms** en LAN, fins a **10× millor** que ActiveX+RTSP (800–2000 ms). Els rangs no se solapen.

**Transició:** "Però molts entorns corporatius bloquen UDP. Calia un pla B que no comprometés l'experiència."

---

### [10:10 — 40s] Slide 17: Orquestració de Vídeo — HLS (Fallback i Gravacions)

**Objectiu d'impacte:** La **dualitat WebRTC/HLS** garanteix vídeo en qualsevol xarxa sense configuració manual.

**Speaking points:**
- HLS té **dos rols**: protocol principal per a **gravacions** (línia de temps interactiva), i *fallback* automàtic per a directe si UDP és bloquejat.
- Transport **TCP/HTTPS**: passa qualsevol tallafocs corporatiu sense configuració de xarxa.
- Commuta automàticament en **≤ 5 s** si no es rep cap frame WebRTC. El canvi és **transparent** per l'operador.
- Latència HLS: 2-5 s — acceptable per a playback de gravacions, menys ideal per a directe però garanteix sempre imatge.

**Transició:** "Fins aquí els protocols. Però per servir H.265 al navegador, calia resoldre un repte tècnic no trivial."

---

### [10:50 — 1:10] Slide 18: El Repte H.265 — Trans-muxing al Backend

**Objectiu d'impacte:** El trans-muxing és **l'aportació tècnica original** del projecte: estalvi de disc sense cap cost de CPU.

**Speaking points:**
- H.265 (HEVC) ocupa un **50% menys de disc** que H.264 per la mateixa qualitat: requisit crític en gravadors 24/7 de 152 càmeres.
- El problema: les gravacions FS2 estan en **blocs bruts sense contenidor** al disc. I molts navegadors no suporten H.265 directament.
- La solució: **trans-muxing** al microservei `la-video-engine`. No és *transcoding* (descomprimir + recomprimir: alta CPU). És canviar l'**embolcall**, no el *bitstream*.
- El procés concret: llegir el bloc FS2 → **injectar les capçaleres SPS/PPS/VPS** (que el FS2 no emmagatzema en cada segment, cal recuperar-les del keyframe anterior) → empaquetar com **MPEG-TS** per a HLS.
- Resultat: **CPU quasi zero** al gravador ARM. Aquesta complexitat va costar **+25 hores** sobre la planificació inicial.

**Transició:** "Resolts el vídeo i la configuració, queda el tercer pilar: les alarmes en temps real."

---

### [12:00 — 1:00] Slide 19: Gestió d'Alarmes — WebSockets i SQLite VFS

**Objectiu d'impacte:** **Cap alarma es pot perdre**, ni quan hi ha un tall de corrent. Dos mecanismes independents en garanteixen la fiabilitat.

**Speaking points:**
- **Notificació (WebSockets push)**: zero polling. Connexió WSS persistent. Quan un sensor salta, el push arriba en **< 1 s** a tots els operadors connectats simultàniament.
- **Persistència (`remote_vfs`)**: driver VFS personalitzat per a SQLite. Les escriptures van per **Unix Domain Socket** al `la-db-engine`, l'únic procés amb permisos de disc. Sectors alineats a **512 bytes**: atomicitat garantida davant talls de corrent.
- Mode **WAL (Write-Ahead Logging)** + `synchronous=NORMAL`: propietats **ACID** completes verificades en simulacions de tall elèctric forçat.
- Reconnexió automàtica del WebSocket amb **backoff exponencial** (1s, 2s, 4s, 8s...): protegeix la CPU embeguda d'un allau de reconnexions.

**Transició:** "Tenim el sistema construït. Ara calen dades. Vegem els resultats de la validació."

---

## BLOC 4: PROVES I VALIDACIÓ
### Temps total: 2 minuts (Slides 20–21)

---

### [13:00 — 1:00] Slide 20: Validació — Latència

**Objectiu d'impacte:** La millora de latència és **objectivament irrefutable**: els rangs de les dues tecnologies no se solapen.

**Speaking points:**
- ActiveX+RTSP (legacy): **800–2000 ms**. WebRTC/WHEP: **150–300 ms**. Els rangs no se solapen. El pitjor cas de WebRTC (300 ms) és millor que el millor cas d'ActiveX (800 ms).
- La causa és estructural: RTSP usa **TCP** amb retransmissions. WebRTC usa **UDP** amb SRTP, on cada paquet va pel camí més ràpid.
- **RNF-01 assolit** amb marge: < 300 ms P50, < 350 ms P95. Validat en entorn LAN amb Intel i3-8100T.
- HLS *fallback*: 2-5 s, acceptable per a playback de gravacions.

**Transició:** "La latència valida el protocol. Vegem ara el rendiment de CPU i l'acceptació dels usuaris."

---

### [14:00 — 1:00] Slide 21: Validació — Rendiment i Usabilitat

**Objectiu d'impacte:** Les dades tancuen el cercle: el sistema és **eficient, robust i percebut com excel·lent** pels usuaris reals.

**Speaking points:**
- **CPU**: 35% de reducció gràcies als Signals. Validat en streaming WebRTC de **2 hores** sense fuites de memòria. Oscil·lació saludable de **45–65 MB** de RAM (el *garbage collector* de V8 fent el seu treball).
- **SUS**: **85.6 / 100** — categoria *Excellent* (> 80 = Bo, > 70 = Acceptable). 8 participants: tècnics d'instal·lació i operadors de seguretat reals de Lanaccess.
- **40% de reducció de temps** per actualitzar firmware. Cap participant va necessitar suport tècnic per configurar una alarma.
- Connexions concurrents: **25 simultànies** validades amb ApacheBench. Zero corrupció VFS en talls elèctrics simulats.

**Transició:** "Passem ara a la demostració, on veureu tot això en funcionament real."

---

## BLOC 5: DEMOSTRACIÓ EN DIRECTE
### Temps total: 5 minuts (Slide 22)

---

### [15:00 — 5:00] Slide 22: Demostració en Directe

**Objectiu d'impacte:** Fer visible i tangible el sistema: **veure** la latència, **veure** el formulari Schema-Driven, **veure** les alarmes push — en viu.

> **Preparació obligatòria** (fer ABANS d'iniciar la presentació):
> - Navegador obert a `https://<ip-nvr>/` en una finestra separada, **minimitzada**.
> - Confirmar que el NVR és accessible des de la xarxa de la sala.
> - Segon navegador en **pestanya incògnit** llest per simular un segon operador.
> - Carpeta amb **screenshots** de cada pas com a pla B si cau la connexió.

**Seqüència (5 minuts en directe):**

1. **(15:00–15:45) — Login**: Alt+Tab, obrir el navegador. "La URL que veieu és la IP directa del gravador: **zero servidors externs**, zero núvol." Iniciar sessió. Si el tribunal pregunta per les cookies: F12 → Application → Cookies → mostrar `HttpOnly = true`.

2. **(15:45–17:00) — Mosaic WebRTC**: Obrir 4 càmeres en mosaic. "**Fixeu-vos en la latència**: moure un objecte davant de la càmera. La imatge respon en menys de 300 ms. L'antiga plataforma ActiveX tardava entre 800 ms i 2 segons."

3. **(17:00–18:00) — Timeline HLS / Gravacions**: Navegar a la pantalla de gravacions. "Aquí la **línia de temps interactiva**. Busco un moment d'ahir..." Fer un *seek* i reproduir uns segons. "Els segments `.ts` els serveix `la-video-engine` directament des del disc del gravador."

4. **(18:00–19:00) — Alarma push**: Activar un sensor físic o relay manual. "Veieu que **sense que l'operador faci res**, la icona canvia en temps real." Passar al segon navegador (pestanya incògnit). "Si hi ha un segon operador connectat simultàniament, **tots dos ho veuen alhora**."

5. **(19:00–20:00) — Motor Schema-Driven**: Entrar a Configuració → Xarxa. "El formulari que veieu — IP, màscara, gateway, DNS — **no l'ha escrit cap enginyer de frontend**. Angular l'ha generat automàticament a partir del JSON Schema que acaba d'enviar el firmware. Si demà surt un nou model de NVR amb 20 paràmetres nous, el frontend els dibuixarà sols."

**Transició:** "Tanquem la demostració. Dediquem els últims minuts als aspectes transversals i les conclusions."

---

## BLOC 6: TRANSVERSALS I CONCLUSIONS
### Temps total: 5 minuts (Slides 23–27)

---

### [20:00 — 1:00] Slide 23: Planificació — Desviacions i Gestió del Risc

**Objectiu d'impacte:** Les desviacions van recaure **exactament** en les aportacions tècniques més innovadores. Això prova que el risc estava ben localitzat.

**Speaking points:**
- Total previst: **540 h**. Total executat: **585 h**. Desviació: **+45 h (8.3%)** — dins del **10% de contingència** previst.
- Desviació 1: **H.265 trans-muxing (+25 h)**. No es va estimar prou la complexitat de recuperar les capçaleres SPS/PPS/VPS en el FS2 propietari.
- Desviació 2: **Motor Schema-Driven (+20 h)**. El nombre de tipus de camp necessaris (IP, llistes dinàmiques, camps condicionats) va ser superior a l'estimat.
- Resultat: cap funcionalitat va ser retallada ni ajornada. La contingència es va usar en un **76%**, no al 100%.

**Transició:** "Aquestes hores extra van tenir un cost, però el pressupost final va romandre per sota del màxim previst."

---

### [21:00 — 45s] Slide 24: Pressupost — Cost Previst vs Executat

**Objectiu d'impacte:** El projecte ha entregat **tot l'abast, a temps, i per sota del pressupost màxim**.

**Speaking points:**
- Total executat: **16.061,50 €** vs 16.420,25 € previstos. **Estalvi net: 358,75 €**.
- Perfil de costos típic de software: >**90% és personal**. El hardware és amortització d'equips existents.
- La contingència del **10%** va absorbir les 45 hores extra sense esgotar-se. Que no s'hagi exhaurit no és un objectiu en si: existia per ser usada.

**Transició:** "Més enllà dels números, el projecte té un impacte real en tres dimensions de sostenibilitat."

---

### [21:45 — 45s] Slide 25: Sostenibilitat i Impacte Social

**Objectiu d'impacte:** La sostenibilitat no és un exercici acadèmic: les dades tècniques del projecte **generen impacte mesurable**.

**Speaking points:**
- **Ambiental**: el **35% de reducció de CPU** en terminals que funcionen 24/7 es tradueix directament en estalvi energètic a escala de flota. La gestió remota elimina desplaçaments de tècnics.
- **Social**: accessibilitat multiplataforma — Linux, Mac, Chrome OS. Operadors que treballaven exclusivament en Windows ara no tenen restriccions. La interfície clara redueix l'estrès en torns de vigilància.
- **Ètica i RGPD**: les imatges mai surten de la xarxa local del client. Cap emmagatzematge biometria. **Cap decisió automatitzada sense supervisió humana**: totes les accions de seguretat les pren sempre una persona.

**Transició:** "Tanquem repasant si hem complert el que ens vam proposar."

---

### [22:30 — 1:30] Slide 26: Conclusions — Objectius Assolits

**Objectiu d'impacte:** No és una llista de *checkboxes*: cada ítem representa un **problema real que ara no existeix**.

**Speaking points:**
- **Plataforma 100% web** sense plugins: un client amb Mac o Linux ja pot accedir als NVR NXT. Internet Explorer ja no és un requisit.
- **Latència < 300 ms**: el requisit de vigilància activa és complert. Les sales de control ja no experimenten el retard de 2 segments de l'antiga plataforma.
- **Motor Schema-Driven**: la contribució arquitectònica duradora. Cada futura versió de firmware del NVR és compatible amb la interfície web des del primer moment.
- **Seguretat OWASP Top 10** i **35% de reducció de CPU**: validats amb dades, no com a estimació.
- Quatre línies de futur identificades: dashboard de reports en temps real, snapshot automàtic en alarma, app mòbil PWA, i anàlisi de vídeo amb IA.

**Transició:** "Amb tot això, tanco la presentació. Gràcies."

---

### [24:00 — 1:00] Slide 27: Gràcies — Torn de Preguntes

**Objectiu d'impacte:** Tancar amb **autoritat i calma**: domines el tema, estàs preparat per a qualsevol pregunta.

**Speaking points:**
- "Moltes gràcies al tribunal per la vostra atenció. Estic preparat per iniciar el torn de preguntes."
- Recordatori ràpid de les tres xifres clau: latència **150–300 ms** WebRTC, **35%** menys CPU, **85.6/100** SUS.
- Postura: mans sobre la taula, contacte visual amb el tribunal, veu ferma. No afegeixis res més. El silenci és d'autoritat.

---

## RESUM DE TEMPS

| Bloc | Slides | Temps | Total |
|------|--------|-------|-------|
| 1 · Context i Problema | 1–4 | 0:00–3:00 | 3 min |
| 2 · Especificació i Disseny | 5–12 | 3:00–7:00 | 4 min |
| 3 · Implementació | 13–19 | 7:00–13:00 | 6 min |
| 4 · Proves i Validació | 20–21 | 13:00–15:00 | 2 min |
| 5 · Vídeo Demo | 22 | 15:00–20:00 | 5 min |
| 6 · Transversals i Conclusions | 23–27 | 20:00–25:00 | 5 min |
| **TOTAL** | **27 slides** | | **25 min** |

---

## PREGUNTES TRAMPA FREQÜENTS — Respostes preparades

### "Per què Pessimistic Lock i no Optimistic Lock?"
> "L'Optimistic Lock deixa que dues escriptures coexisteixin i resol el conflicte *a posteriori*. En configuració de xarxa d'un gravador de seguretat, una escriptura que deixes l'equip en estat inconsistent pot fer-lo inaccessible. El cost d'un *pessimistic lock* — un tècnic en mode lectura mentre un altre configura — és negligible comparat amb el risc d'una IP corrupte."

### "SQLite en un sistema embegut no és massa pesat?"
> "SQLite és el motor de base de dades més desplegat del món en dispositius embeguts — Android, iOS, Firefox tots el fan servir. El VFS personalitzat és la nostra capa de control que garanteix l'alineació de sectors i l'atomicitat específics del nostre hardware. No estem inventant una base de dades; estem configurant-ne una de provada per al nostre cas d'ús."

### "Per què no usar JWT en comptes de cookies?"
> "JWT al LocalStorage és trivial de robar per XSS: qualsevol script injectat pot fer `localStorage.getItem('token')`. Una cookie HttpOnly és *invisible* per a JavaScript: cap codi pot llegir-la. En un sistema de seguretat amb accés a càmeres d'infraestructura crítica, no accepto aquell risc."

### "La reducció del 35% de CPU no és una dada massa genèrica?"
> "No. És una dada mesura en un hardware concret: Intel Core i3-8100T, 4 nuclis a 3.10 GHz, 8 GB RAM, Windows 10 IoT, streaming de 4 càmeres simultànies amb alertes WebSocket actives. Comparat amb un prototip equivalent basat en Zone.js + RxJS en el mateix hardware, la reducció és del 35% en el fil principal del navegador."

### "H.265 als navegadors no és un problema de patents?"
> "El problema de patents és real però ortogonal al nostre cas: el *transcoding* H.265 requeriria una llicència HEVC. El *trans-muxing* no recodifica: simplement canvia el contenidor. El client que descodifica és el navegador o el sistema operatiu del client, que ja gestiona les llicències de descodificació per compte propi."
