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

**Objectiu d'impacte:** Fixar la restricció de disseny més important: tot ha de córrer **sense infraestructura centralitzada**, dins del propi gravador.

**Speaking points:**
- Lanaccess Telecom, SA fabrica gravadors NVR professionals — la família **NXT** — per a gestió de videovigilància IP.
- La solució s'executa **directament al hardware del gravador**: zero servidors externs, zero núvol.
- La restricció clau: la CPU del dispositiu és **compartida** entre la descodificació de vídeo, l'escriptura a disc i el servei de la interfície web. Cada cicle compta.
- La paraula clau és **standalone**: el gravador NXT és l'únic servidor disponible.

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
### Temps total: 3 min 40s (Slides 5–8)

---

### [3:00 — 20s] Slide 5: Els Actors del Sistema

**Objectiu d'impacte:** Transmetre que el gravador no és només hardware; és un **actor actiu** que dicta les regles.

**Speaking points:**
- Tres actors: **Administrador** (configuració total), **Operador** (vigilància dia a dia), i el propi **Sistema NVR** com a actor actiu.
- El NVR emet alarmes, dicta l'esquema JSON i gestiona els *config locks*: no és passiu.
- Set casos d'ús derivats: autenticació, actualització de firmware, gravacions, vídeo directe, alarmes, configuració dinàmica, administració.

**Transició:** "D'aquests actors vam derivar els requisits no funcionals crítics, que van determinar tota l'arquitectura."

---

### [3:20 — 1:10] Slide 6: Requisits Crítics — Rendiment, Vídeo i Seguretat

**Objectiu d'impacte:** Els RNF no eren aspiracionals: eren **restriccions de disseny dures** que van forçar decisions concretes tant de rendiment com de seguretat.

**Speaking points:**
- **RNF-01**: latència de vídeo **< 300 ms** en P50, < 350 ms en P95. En una sala de control activa, 2 segons de retard és inacceptable.
- **RNF-02**: si UDP bloquejat, **fallback automàtic a HLS en ≤ 5 s** — transparent per l'operador.
- **RNF-03**: zero polling HTTP, actualitzacions granulars via Signals. La CPU compartida del dispositiu no admet overhead innecessari.
- **Zero LocalStorage** per a credencials: cookies **HttpOnly + Secure + SameSite=Strict**. La seguretat és una restricció de disseny, no una capa afegida al final.
- **TLS 1.2/1.3**, **Privacy by Design** i compliment **OWASP Top 10** com a criteri d'acceptació formal.

**Transició:** "Amb els requisits clars, dissenyem l'arquitectura que els satisfà."

---

### [4:30 — 1:20] Slide 7: Arquitectura Global i Microserveis

**Objectiu d'impacte:** L'arquitectura no és una llista de tecnologies; és una **resposta deliberada a les restriccions de recursos** del dispositiu.

**Speaking points:**
- La **SPA Angular** s'executa al navegador del client: allibera la CPU del gravador per a vídeo i disc.
- Al backend, **microserveis C++ especialitzats**: `la-core-legacy` (Config API, Auth), `la-db-engine` (SQLite via gRPC), `la-core-notifier` i `la-core-alarm` (WebSockets push), `la-video-engine` + **MediaMTX** (WebRTC/WHEP i HLS). Si un procés falla, la resta segueixen funcionant.
- Comunicació interna per **Unix Domain Sockets**: el canal IPC més ràpid de Linux, sense passar per NGINX.
- **NGINX** com a únic punt d'entrada al port 443: imposa les regles de seguretat a tota la plataforma i oculta els ports interns. Tots gestionats per **systemd** sobre Linux.

**Transició:** "NGINX és la frontera de seguretat. Vegem ara com s'implementa tota la lògica de configuració i vídeo."

---

### [5:50 — 50s] Slide 8: NGINX com a Frontera de Seguretat i el Problema de Concurrència

**Objectiu d'impacte:** NGINX garanteix la **seguretat per disseny**, i la concurrència en configuració és un *race condition* real amb conseqüències greus.

**Speaking points:**
- Port **443** com a únic punt d'entrada: NGINX gestiona les cookies amb **HttpOnly + SameSite=Strict** — el JavaScript mai pot llegir la sessió. Elimina d'arrel **OWASP A07**.
- Problema de concurrència: dos admins editen simultàniament la IP del gravador. Sense control, **s'aplica l'últim que escriu** i els canvis de l'altre s'esfumen silenciosament.
- En xarxes de seguretat amb múltiples tècnics, aquesta col·lisió no és teòrica: ocorre. Resultat: dispositiu en **estat inconsistent**, possiblement inaccessible per xarxa.

**Transició:** "Amb l'arquitectura i el perímetre de seguretat dissenyats, passem al cor de la implementació."

---

## BLOC 3: IMPLEMENTACIÓ
### Temps total: 6 min 20s (Slides 9–12)

---

### [6:40 — 1:40] Slide 9: Pàgina de Directe — Vídeo en Temps Real

**Objectiu d'impacte:** El mosaic de càmeres és la pàgina més crítica: latència imperceptible o el sistema és inútil en una sala de control.

**Speaking points:**
- Quan l'operador obre la pàgina de directe, Angular fa un **POST a `/stream/<cam>/whep`**. Res més: **WHEP** (RFC 9716) estandaritza la negociació WebRTC *egress* amb una sola crida HTTP.
- Transport: **SRTP/UDP + DTLS** per xifrat, **ICE** (RFC 8445) per travessia de NATs i tallafocs. Resultat: **150–300 ms** de latència. L'antiga plataforma ActiveX: **800–2000 ms**. Els rangs **no se solapen**: el pitjor cas de WebRTC és millor que el millor cas d'ActiveX.
- Si UDP és bloquejat per un firewall corporatiu: **fallback automàtic a HLS en ≤ 5 s**, transparent per l'operador. TCP/HTTPS passa qualsevol restricció de xarxa.
- **Angular Signals**: quan arriba un frame de la càmera 3, s'actualitza **únicament** el component d'aquella càmera. Zone.js hauria rellegit tot el DOM. Resultat mesurat: **35% menys CPU** en un terminal Intel Core i3-8100T, Windows 10 IoT, 4 càmeres en continu.

**Transició:** "El directe resolt. La pàgina de gravacions presenta un repte diferent: servir H.265 a un navegador estàndard."

---

### [8:20 — 1:20] Slide 10: Pàgina de Gravacions — Timeline i H.265

**Objectiu d'impacte:** La **línia de temps interactiva** sembla simple per l'operador; darrere hi ha un repte de trans-muxing no trivial.

**Speaking points:**
- L'operador selecciona un segment i reprodueix. El que veu és fluid; el que passa al darrere és complex.
- Les gravacions al disc estan en format **FS2 propietari**: blocs bruts sense contenidor, sense capçaleres estàndard. Cap reproductor web pot llegir-los directament.
- El microservei `la-video-engine` fa **trans-muxing** en temps real: llegeix el bloc FS2, recupera les capçaleres **SPS/PPS/VPS** del keyframe anterior, i empaqueta tot com **MPEG-TS** per servir-lo via HLS/TCP.
- **Trans-muxing ≠ transcoding**: no descomprimim ni recomprimim. Canviem l'embolcall, no el bitstream. **CPU quasi zero** al gravador. El transcoding hauria estat inviable en hardware embegut.
- H.265 (HEVC): **50% menys disc** que H.264 per la mateixa qualitat. Factor crític en gravadors que funcionen 24/7.

**Transició:** "Amb el vídeo resolt, la pàgina d'alarmes és la que exigeix la màxima fiabilitat: cap alerta es pot perdre."

---

### [9:40 — 1:10] Slide 11: Pàgina d'Alarmes — Push i Persistència

**Objectiu d'impacte:** **Cap alarma es pot perdre**, ni en talls de corrent. Dos mecanismes independents en garanteixen la fiabilitat.

**Speaking points:**
- Quan un sensor salta, `la-core-alarm` fa un **push WSS** (WebSocket segur) persistent a tots els operadors connectats. La icona canvia **en < 1 s** sense que l'operador faci res. Gràcies a Signals, s'actualitza **una icona**, no tot el DOM.
- Reconnexió automàtica amb **backoff exponencial** (1s, 2s, 4s, 8s...): protegeix la CPU del gravador d'allaus de reconnexions simultànies si cau la xarxa.
- **Persistència**: driver VFS personalitzat per a **SQLite** (`remote_vfs`). Les escriptures van per **Unix Domain Socket** al `la-db-engine`. Sectors alineats a **512 bytes**: atomicitat garantida davant talls de corrent.
- Mode **WAL + `synchronous=NORMAL`**: propietats **ACID** completes, verificades en simulacions de tall elèctric forçat. Zero corrupció en tots els escenaris provats.

**Transició:** "I la quarta pàgina conté la contribució arquitectònica més innovadora del projecte."

---

### [10:50 — 2:10] Slide 12: Pàgina de Configuració — Schema-Driven i Config Lock

**Objectiu d'impacte:** El formulari que l'usuari veu és **100% generat automàticament**. Cap enginyer de frontend ha escrit cap camp de configuració.

**Speaking points:**
- El problema de la plataforma legacy: **cada canvi de firmware requeria modificar el frontend** manualment. Fre directe al time-to-market.
- La solució — **motor Schema-Driven**: el backend no envia dades, envia **metadades**. Cada camp porta `_type`, `_allowedValues`, `_min`, `_max`, `_default`. Angular llegeix l'esquema i renderitza el formulari complet.
- Exemple real: `ip_address` amb `_type: "ipaddr"` → input amb **regex de validació IP**. `codec` amb `_allowedValues: ["H264","H265"]` → **`<select>`** automàtic. `max_cameras` amb `_min: 1`, `_max: 152` → input numèric [1,152] ja configurat. **10 tipus de camp** que cobreixen el 100% del firmware.
- **Config Lock pessimista**: quan un tècnic entra a configurar, **POST `/lock`** retorna un token **32-char hex**. Tota escriptura posterior ha d'incloure'l. Si tanca el portàtil, el lock **expira en 60-120 s** sol — cap sistema bloquejat per sempre. Altres usuaris reben **HTTP 423 Locked** i resten en mode lectura automàticament.
- Impacte: nou model NVR amb 20 paràmetres nous → **zero canvis al frontend**. Zero PR, zero deploy, zero cost.

**Transició:** "Quatre pàgines, quatre problemes resolts. Vegem ara les dades que validen que tot funciona."

---

## BLOC 4: PROVES I VALIDACIÓ
### Temps total: 2 minuts (Slide 13)

---

### [13:00 — 2:00] Slide 13: Validació — Latència, Rendiment i Usabilitat

**Objectiu d'impacte:** Les dades tancuen el cercle: la millora de latència és **objectivament irrefutable**, i el sistema és **eficient i percebut com excel·lent** pels usuaris reals.

**Speaking points:**
- **Latència**: ActiveX+RTSP (legacy): **800–2000 ms**. WebRTC/WHEP: **150–300 ms**. Els rangs no se solapen: el pitjor cas de WebRTC (300 ms) és millor que el millor cas d'ActiveX (800 ms). **RNF-01 assolit** amb marge.
- **CPU**: 35% de reducció gràcies als Signals. Validat en streaming WebRTC de **2 hores** sense fuites de memòria. Oscil·lació saludable de **45–65 MB** de RAM.
- **SUS**: **85.6 / 100** — categoria *Excellent* (> 80 = Bo, > 70 = Acceptable). 8 participants: tècnics d'instal·lació i operadors de seguretat reals de Lanaccess.
- **40% de reducció de temps** per actualitzar firmware. **25 connexions concurrents** validades. Zero corrupció VFS en talls elèctrics simulats.

**Transició:** "Passem ara a la demostració, on veureu tot això en funcionament real."

---

## BLOC 5: DEMOSTRACIÓ EN DIRECTE
### Temps total: 5 minuts (Slide 14)

---

### [15:00 — 5:00] Slide 14: Demostració en Directe

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
### Temps total: 5 minuts (Slides 15–19)

---

### [20:00 — 1:00] Slide 15: Planificació — Desviacions i Gestió del Risc

**Objectiu d'impacte:** Les desviacions van recaure **exactament** en les aportacions tècniques més innovadores. Això prova que el risc estava ben localitzat.

**Speaking points:**
- Total previst: **540 h**. Total executat: **585 h**. Desviació: **+45 h (8.3%)** — dins del **10% de contingència** previst.
- Desviació 1: **H.265 trans-muxing (+25 h)**. No es va estimar prou la complexitat de recuperar les capçaleres SPS/PPS/VPS en el FS2 propietari.
- Desviació 2: **Motor Schema-Driven (+20 h)**. El nombre de tipus de camp necessaris (IP, llistes dinàmiques, camps condicionats) va ser superior a l'estimat.
- Resultat: cap funcionalitat va ser retallada ni ajornada. La contingència es va usar en un **76%**, no al 100%.

**Transició:** "Aquestes hores extra van tenir un cost, però el pressupost final va romandre per sota del màxim previst."

---

### [21:00 — 45s] Slide 16: Pressupost — Cost Previst vs Executat

**Objectiu d'impacte:** El projecte ha entregat **tot l'abast, a temps, i per sota del pressupost màxim**.

**Speaking points:**
- Total executat: **16.061,50 €** vs 16.420,25 € previstos. **Estalvi net: 358,75 €**.
- Perfil de costos típic de software: >**90% és personal**. El hardware és amortització d'equips existents.
- La contingència del **10%** va absorbir les 45 hores extra sense esgotar-se.

**Transició:** "Més enllà dels números, el projecte té un impacte real en tres dimensions de sostenibilitat."

---

### [21:45 — 45s] Slide 17: Sostenibilitat i Impacte Social

**Objectiu d'impacte:** La sostenibilitat no és un exercici acadèmic: les dades tècniques del projecte **generen impacte mesurable**.

**Speaking points:**
- **Ambiental**: el **35% de reducció de CPU** en terminals que funcionen 24/7 es tradueix directament en estalvi energètic a escala de flota. La gestió remota elimina desplaçaments de tècnics.
- **Social**: accessibilitat multiplataforma — Linux, Mac, Chrome OS. La interfície clara redueix l'estrès en torns de vigilància.
- **Ètica i RGPD**: les imatges mai surten de la xarxa local del client. **Cap decisió automatitzada sense supervisió humana**: totes les accions de seguretat les pren sempre una persona.

**Transició:** "Tanquem repasant si hem complert el que ens vam proposar."

---

### [22:30 — 1:30] Slide 18: Conclusions — Objectius Assolits

**Objectiu d'impacte:** No és una llista de *checkboxes*: cada ítem representa un **problema real que ara no existeix**.

**Speaking points:**
- **Plataforma 100% web** sense plugins: un client amb Mac o Linux ja pot accedir als NVR NXT. Internet Explorer ja no és un requisit.
- **Latència < 300 ms**: el requisit de vigilància activa és complert. Les sales de control ja no experimenten el retard de l'antiga plataforma.
- **Motor Schema-Driven**: la contribució arquitectònica duradora. Cada futura versió de firmware és compatible amb la interfície web des del primer moment.
- **Seguretat OWASP Top 10** i **35% de reducció de CPU**: validats amb dades, no com a estimació.
- Quatre línies de futur identificades: dashboard de reports en temps real, snapshot automàtic en alarma, app mòbil PWA, i anàlisi de vídeo amb IA.

**Transició:** "Amb tot això, tanco la presentació. Gràcies."

---

### [24:00 — 1:00] Slide 19: Gràcies — Torn de Preguntes

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
| 2 · Especificació i Disseny | 5–8 | 3:00–6:40 | 3 min 40s |
| 3 · Implementació | 9–12 | 6:40–13:00 | 6 min 20s |
| 4 · Proves i Validació | 13 | 13:00–15:00 | 2 min |
| 5 · Vídeo Demo | 14 | 15:00–20:00 | 5 min |
| 6 · Transversals i Conclusions | 15–19 | 20:00–25:00 | 5 min |
| **TOTAL** | **19 slides** | | **25 min** |

---

## PREGUNTES TRAMPA FREQÜENTS — Respostes preparades

### "Per què Pessimistic Lock i no Optimistic Lock?"
> "L'Optimistic Lock deixa que dues escriptures coexisteixin i resol el conflicte *a posteriori*. En configuració de xarxa d'un gravador de seguretat, una escriptura que deixes l'equip en estat inconsistent pot fer-lo inaccessible. El cost d'un *pessimistic lock* — un tècnic en mode lectura mentre un altre configura — és negligible comparat amb el risc d'una IP corrupte."

### "SQLite en un sistema embegut no és massa pesat?"
> "SQLite és el motor de base de dades més desplegat del món en dispositius embeguts — Android, iOS, Firefox tots el fan servir. El VFS personalitzat és la nostra capa de control que garanteix l'alineació de sectors i l'atomicitat específics del nostre hardware. No estem inventant una base de dades; estem configurant-ne una de provada per al nostre cas d'ús."

### "Per què no usar JWT en comptes de cookies?"
> "JWT al LocalStorage és trivial de robar per XSS: qualsevol script injectat pot fer `localStorage.getItem('token')`. Una cookie HttpOnly és *invisible* per a JavaScript: cap codi pot llegir-la. En un sistema de seguretat amb accés a càmeres d'infraestructura crítica, no accepto aquell risc."

### "La reducció del 35% de CPU no és una dada massa genèrica?"
> "No. És una dada mesurada en un hardware concret: Intel Core i3-8100T, 4 nuclis a 3.10 GHz, 8 GB RAM, Windows 10 IoT, streaming de 4 càmeres simultànies amb alertes WebSocket actives. Comparat amb un prototip equivalent basat en Zone.js + RxJS en el mateix hardware, la reducció és del 35% en el fil principal del navegador."

### "H.265 als navegadors no és un problema de patents?"
> "El problema de patents és real però ortogonal al nostre cas: el *transcoding* H.265 requeriria una llicència HEVC. El *trans-muxing* no recodifica: simplement canvia el contenidor. El client que descodifica és el navegador o el sistema operatiu del client, que ja gestiona les llicències de descodificació per compte propi."
