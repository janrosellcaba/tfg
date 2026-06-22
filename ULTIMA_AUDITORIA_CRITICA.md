# Última Auditoria Crítica — TFG *Web Core View*

**Rol:** Tribunal avaluador sènior (FIB–UPC) + control de qualitat editorial acadèmica
**Abast:** `main.tex` + `capitols/{1..8}.tex` (annexos buit)
**Data:** 22 de juny de 2026
**Regla aplicada:** Cap fitxer `.tex` ha estat modificat. Aquest informe és exclusivament de lectura, anàlisi i recomanació.

---

## 0. Veredicte executiu

El document és **sòlid, madur i amb una traçabilitat creuada notable** (els RNF s'enllacen amb la validació, el pressupost quadra a l'euro, les decisions de disseny estan justificades). La bibliografia està neta: les **43 claus `\cite` resolen** totes contra `references.bib` i el `.log` **no reporta cap referència ni citació indefinida**.

Tanmateix, una lectura adversarial detecta **3 punts crítics** que un tribunal tècnic competent pot explotar a la defensa, concentrats en tres àrees: (1) les xifres del *Config Lock* no quadren entre capítols, (2) una promesa explícita de contingut no es compleix (detecció de còdec H.265→H.264), i (3) una contradicció sobre el mecanisme de senyalització WebRTC (WHEP vs WebSocket). Es detallen a continuació, ordenats per severitat.

---

## 1. CRÍTIC

### C-1 · Les xifres del *Config Lock* (`expiresIn`) es contradiuen entre capítols
**Fitxers:** `1_introduccio.tex:132` · `3_requisits.tex:347` (RNF-05) · `3_requisits.tex:435` (model) · `5_implementacio.tex:592,610`

- **Introducció (ch1:132):** «un temps de vigència (`expiresIn`) de **30 o 120 segons**… el frontend el renova a **la meitat** del seu període» → renovació a **15 s o 60 s**.
- **RNF-05 (ch3:347):** «el lock es renova cada `expiresIn/2` **(30 s o 60 s)**» → això implica `expiresIn` ∈ **{60, 120}** i renovació a 30 s o 60 s.
- **Implementació (ch5:592):** l'exemple JSON mostra `"expiresIn": 30`, i el text (ch5:610) diu renovació «cada `expiresIn/2`» → **15 s** per a aquest exemple.

**Problema:** per al node "curt", la Introducció el tracta com a `expiresIn = 30` (renovació 15 s), mentre que RNF-05 tracta el "30" com a `expiresIn/2` (és a dir `expiresIn = 60`, renovació 30 s). Els dos conjunts —{30, 120} vs {60, 120}— **són incompatibles**, i l'exemple d'implementació (30) no encaixa net amb cap dels dos relats.

**Recomanació:** fixar una única taula de veritat (p. ex. *node estàndard* `expiresIn=60`, *node crític* `expiresIn=120`) i propagar-la idènticament a ch1, RNF-05, el model conceptual i l'exemple JSON de ch5. És el tipus d'incoherència numèrica que un avaluador subratlla immediatament perquè apareix en un requisit formalitzat (VOLERE).

---

### C-2 · Promesa de contingut incomplerta: detecció de capacitats i commutació H.265 → H.264
**Fitxers:** `1_introduccio.tex:87` (promesa) · `1_introduccio.tex:171` (obstacle) · `5_implementacio.tex:875–885` (contingut real)

- **Introducció (ch1:87):** «el sistema prioritza H.265 quan la càmera i el terminal client ho suporten… i **commuta automàticament a H.264 en cas contrari**. **Els detalls de la detecció de capacitats i la lògica de commutació s'expliquen al Capítol 5.**»
- **Obstacle (ch1:171):** parla del «disseny de la **lògica de *fallback* automàtic a H.264**».

**Problema:** el Capítol 5 (§*Descodificació de còdecs H.264 i H.265*) descriu únicament el *trans-muxing* al backend i la **inserció de capçaleres SPS/PPS/VPS** dins del flux HLS de gravacions. **Enlloc** es descriu (i) com es detecta si el navegador client suporta H.265, ni (ii) la lògica que commuta el flux **en directe** de H.265 a H.264. La cerca a tot el document confirma que l'única «commutació» documentada és **WebRTC→HLS** (caiguda de xarxa, RNF-02), **no** H.265→H.264 (capacitat de còdec). La promesa explícita «s'expliquen al Capítol 5» queda, doncs, **sense complir**.

**Recomanació:** o bé afegir la secció promesa (com es consulta `canPlayType`/`isTypeSupported` o l'SDP negociat, i on es força el perfil H.264), o bé **suavitzar la promesa** de la Introducció perquè no anunciï un contingut inexistent. Tal com està, és una pregunta de defensa gairebé garantida: *«On és exactament la lògica de detecció de capacitats H.265 que prometeu al Capítol 5?»*.

---

### C-3 · Contradicció en la senyalització WebRTC: WHEP (HTTP) vs WebSocket
**Fitxers:** `3_requisits.tex:153,157` (UC-03) · `5_implementacio.tex:725` · `4_arquitectura.tex:94,114,180`

- **Requisits + Implementació:** la negociació és per **WHEP** — «negociació SDP via **WHEP**» (ch3:153) i «POST WHEP a `/stream/<camera_id>/whep`» (ch3:157); ch5:725 confirma «MediaMTX… servidor de WebRTC i **WHEP** (*WebRTC HTTP Egress Protocol*)». WHEP és, per definició, **HTTP POST** (RFC 9716).
- **Arquitectura:** descriu el mateix camí `/stream/` com a senyalització **per WebSocket**: «senyalització **sobre WebSocket**» (ch4:94), la matriu de comunicació marca `/stream/` com a **«WSS + SRTP/UDP»** (ch4:114), i el fragment NGINX (ch4:180) imposa capçaleres `Upgrade`/`Connection: upgrade` (és a dir, *upgrade* a WebSocket) a `/stream/`.

**Problema:** WHEP (HTTP POST) i la senyalització per WebSocket són **mecanismes diferents i mútuament excloents** per a la negociació SDP. El document afirma alhora tots dos per al mateix endpoint. A més, forçar l'*upgrade* WebSocket sobre `/stream/` és incoherent amb un flux WHEP pur basat en POST.

**Recomanació:** decidir quin mecanisme s'usa realment (si és WHEP, eliminar la menció a «senyalització sobre WebSocket» de ch4 i les capçaleres `Upgrade` del `/stream/` al llistat, i corregir la matriu de `WSS` a `HTTPS`). Un membre del tribunal amb base de WebRTC detectarà la incoherència sense esforç.

---

## 2. MODERAT

### M-1 · El VFS de SQLite i FS2: «integració directa» (ch1) vs «subsistemes independents» (ch6)
**Fitxers:** `1_introduccio.tex:80` · `4_arquitectura.tex:60,70` · `5_implementacio.tex:851` · `6_validacio.tex:93`

- **Introducció (ch1:80):** el VFS «ha fet possible **integrar el motor de base de dades directament amb el sistema de fitxers propietari de Lanaccess (FS2)**, garantint l'atomicitat de les escriptures sobre la partició *raw*».
- **Validació (ch6:93):** «**Són dos subsistemes independents** amb responsabilitats i tecnologies diferenciades» — FS2 per als fotogrames de vídeo; `remote_vfs` per a les dades relacionals via *socket* cap a `la-video-engine`.
- **Arquitectura (ch4:70):** el VFS «verifica l'offset físic a la **partició relacional** del disc» (≠ FS2).

**Problema:** la Introducció afirma que el VFS de SQLite *s'integra directament amb FS2*; la Validació afirma explícitament el contrari (independents). És una contradicció de fons sobre com funciona la persistència, en un punt que el TFG ven com a aportació tècnica.

**Recomanació:** alinear ch1 amb la descripció (correcta i més precisa) de ch4/ch6: el `remote_vfs` canalitza les escriptures relacionals via *socket* Unix cap a `la-video-engine`, que les escriu sobre una partició *raw* dedicada; **FS2 és un subsistema diferent**. Reescriure la frase de ch1:80 perquè no digui «integrar… directament amb FS2».

### M-2 · Es reivindica compatibilitat amb Safari fora de l'abast validat
**Fitxers:** `8_conclusions.tex:17` · `3_requisits.tex:380` (RNF-07) · `6_validacio.tex:105–111`

- **Conclusions (ch8:17):** «una interfície Angular 19 que funciona de forma nativa a **Chrome, Firefox, Edge i Safari**».
- **RNF-07 (ch3:380):** l'abast de compatibilitat és «les dues darreres versions estables de **Google Chrome, Mozilla Firefox i Microsoft Edge**» — **Safari no hi és**.
- **Validació (UAT, ch6):** els criteris d'acceptació de RNF-07 només cobreixen Chrome/Firefox/Edge.

**Problema:** les Conclusions afirmen una compatibilitat (Safari) que **no és requisit ni s'ha validat**. És una sobre-afirmació fàcilment rebatible, especialment delicada perquè Safari té un comportament de còdecs/WebRTC particular.

**Recomanació:** o afegir Safari a RNF-07 i a la UAT, o retirar Safari de la frase de Conclusions per cenyir-se a l'abast realment validat.

### M-3 · Finestra d'invalidació del *keep-alive*: 90 s (RNF) vs 120 s (implementació)
**Fitxers:** `3_requisits.tex:364` (RNF-06) · `5_implementacio.tex:232`

- **RNF-06 (ch3:364):** «El servidor invalida la sessió si no rep cap *keep-alive* en **> 90 s**».
- **Implementació (ch5:232):** «si el client no ha realitzat un *ping* durant **dos períodes consecutius**» → amb període de 60 s, això són **120 s**.

**Problema:** 90 s (criteri d'acceptació) ≠ 120 s (comportament descrit). Discrepància numèrica directa entre un RNF i la seva implementació.

**Recomanació:** unificar (p. ex. invalidació a 90 s = 1,5 períodes, o reescriure RNF-06 a «> 120 s»).

### M-4 · Nombre màxim de càmeres: 150 vs 151 vs 152
**Fitxers:** `3_requisits.tex:425` (model) · `5_implementacio.tex:489` (schema) · `4_arquitectura.tex:290`

- **Model conceptual (ch3:425):** «un NVR pot tenir entre 0 i un màxim de **151 càmeres**».
- **Schema JSON (ch5:489):** `"indexFrom": 0, "indexTo": 151` → índexs 0–151 = **152 instàncies**.
- **Arquitectura (ch4:290):** «una unitat de rack de **150 càmeres**».

**Problema:** tres xifres diferents per a la mateixa capacitat. `indexTo:151` amb `indexFrom:0` són 152 càmeres, no 151; i ch4 arrodoneix a 150.

**Recomanació:** decidir el límit real i expressar-lo consistentment (si el rang és 0–151 inclusiu, són 152; si el màxim són 150 càmeres, l'índex hauria d'anar fins a 149).

### M-5 · Acrònims usats al Capítol 3 abans de la seva definició
**Fitxers:** `3_requisits.tex:153,157,158,162` → definits més tard a `4_arquitectura.tex:63` i `5_implementacio.tex:725`

- **WHEP** apareix a ch3:153 sense desplegar; es desplega a ch5:725 («*WebRTC HTTP Egress Protocol*»).
- **SDP** apareix a ch3:153 sense desplegar; es desplega a ch5:725 («*Session Description Protocol*»).
- **ICE** apareix a ch3:158/162 sense desplegar; es desplega a ch4:63 («*Interactive Connectivity Establishment*»).

**Problema:** la convenció acadèmica és desplegar l'acrònim **en la primera aparició**. Aquí el lector troba WHEP/SDP/ICE als casos d'ús (ch3) i no en rep la definició fins a dos capítols després.

**Recomanació:** desplegar WHEP, SDP i ICE a la seva primera aparició al Capítol 3 (o, com a mínim, afegir una taula d'acrònims al preàmbul).

### M-6 · El servei `ttyd` (terminal web `/terminal/`) és un cos estrany respecte al discurs de seguretat
**Fitxers:** `4_arquitectura.tex:96,118`

`/terminal/` → `ttyd` (port 7681) «ofereix una terminal interactiva accessible directament des del navegador» (ch4:96) i apareix a la matriu (ch4:118), però **no es torna a esmentar enlloc** i, al fragment NGINX del Llistat de rutes, **no es mostra protegida amb `auth_request`** (a diferència de `/video-engine/`, `/db-engine/`, `/stream/`).

**Problema:** exposar un *shell* interactiu complet via navegador és una superfície d'atac de primer ordre que **xoca amb tot el relat «OWASP / Privadesa per Disseny / seguretat defensiva»** dels capítols 3 i 4. A més és una funcionalitat òrfena (esmentada una vegada, mai justificada).

**Recomanació:** o bé justificar-la i documentar-ne explícitament la protecció (`auth_request` + rol administrador), o bé retirar-la de la matriu si no forma part de l'abast del producte. Tal com està, convida la pregunta: *«Com es concilia un shell web obert amb la vostra postura de seguretat?»*.

### M-7 · `la-db-engine`: gRPC «als seus clients» però accés HTTPS des del navegador
**Fitxers:** `4_arquitectura.tex:60,117`

Ch4:60 diu que `la-db-engine` «exposa una interfície **gRPC** als seus clients»; la matriu (ch4:117) ruta `Navegador → /db-engine/ → la-db-engine` per **HTTPS** per a «consultes de dades històriques». Un navegador no consumeix gRPC (HTTP/2 binari) directament sense una capa *grpc-web*.

**Problema:** ambigüitat sobre si el navegador parla HTTPS/REST o gRPC amb `la-db-engine`. No és necessàriament un error (pot exposar les dues interfícies), però tal com es redacta sembla contradictori.

**Recomanació:** aclarir que gRPC és la interfície **interna** (cap a `la-core-alarm`) i que el navegador accedeix per una API HTTP diferent, o eliminar la fila `/db-engine/` de la matriu del navegador si l'accés històric va realment per `/video-engine/`.

---

## 3. DETALL / NITPICK

### N-1 · Ús inconsistent de l'espai dur abans de `\cite`
**Abast:** tot el document (`~\cite`: 27 ocurrències · ` \cite` amb espai normal: 23 ocurrències)
El Capítol 1 usa gairebé sempre espai normal (` \cite`), mentre ch4 barreja les dues formes **dins d'una mateixa frase**: `4_arquitectura.tex:269` → `RxJS~\cite{rxjs_docs}` … `granular \cite{learning_angular}`.
**Recomanació:** unificar a `~\cite` (espai dur) a tot arreu, per evitar que un número de citació caigui sol a l'inici de línia. Afecta ch1:14,25,27,29,50,60,71,100,106,115,123; ch2:20; ch3:449; ch4:186,269; ch7:32,92,93.

### N-2 · Les dues taules de rutes NGINX no coincideixen
**Fitxers:** `4_arquitectura.tex:108–121` (`tab:matriu_comunicacio`) vs `5_implementacio.tex:40–50` (`tab:nginx_routes`)
La taula de ch5 **omet** `/db-engine/`, `/terminal/` i `/onvif/` que sí apareixen a ch4; i el *login* es nomena `/login/` + `/api.rest/` a ch4 però `/WebLogin` a ch5. Cap és incorrecta per separat, però el lector que les compari veurà dues "fonts de veritat" divergents.
**Recomanació:** fer que la taula de ch5 sigui un superconjunt coherent de la de ch4, o referenciar-ne una des de l'altra.

### N-3 · Incorrecció normativa: «degut a»
**Fitxer:** `5_implementacio.tex:876` — «…controls ActiveX **degut a** restriccions de patents».
En català normatiu, *degut a* no és correcte com a locució causal. **Recomanació:** «**a causa de** restriccions de patents» / «pel fet de».

### N-4 · Calc «a nivell de» (recurrent)
**Fitxers:** `2_gestio.tex:126` · `4_arquitectura.tex:68,232,278` · `5_implementacio.tex:851,1138`
*A nivell de* com a sinònim de «pel que fa a / quant a / en l'àmbit de» és un calc desaconsellat (només és correcte quan hi ha nivells reals). **Recomanació:** substituir per «pel que fa a», «quant a» o «en l'àmbit de» segons el cas.

### N-5 · «microcontrolador» vs SoC d'aplicacions
**Fitxers:** `5_implementacio.tex:122` («microcontrolador local») vs `5_implementacio.tex:13` (SoC **NXP i.MX**, ARM)
Un i.MX és un **processador d'aplicacions** (SoC), no un microcontrolador. El terme «microcontrolador» (ch5:122) és imprecís i contradiu la descripció correcta de ch5:13. **Recomanació:** usar «SoC», «processador embedded» o «dispositiu».

### N-6 · Lleugera imprecisió de xifratge WebRTC a la Introducció
**Fitxer:** `1_introduccio.tex:70` — «transport sobre UDP **sota xifratge DTLS**».
La resta del document (ch3:460, ch4) és més precisa: **DTLS** per a l'intercanvi de claus/control i **SRTP** per al mèdia. La frase de ch1:70 omet SRTP i pot semblar que el mèdia viatja sobre DTLS. **Recomanació:** «…sota DTLS-SRTP» o esmentar tots dos.

### N-7 · Atribució de fase del *buffer* H.265 ambigua
**Fitxers:** `2_gestio.tex:144` (problema del buffer H.265 a **Gravacions/HLS**, fase GV) vs `2_gestio.tex:256` («buffer i reproducció H.265 (**fase AV**)»).
El mateix problema tècnic s'atribueix a dues fases diferents. **Recomanació:** unificar a quina fase pertany realment el sobrecost del buffer H.265.

### N-8 · «factor entre cinc i deu» (millora de latència)
**Fitxer:** `8_conclusions.tex:21`. Amb ActiveX 800–2000 ms i WebRTC 150–300 ms, el factor real va de ~3,3 (800/300, pitjor cas) a ~13 (2000/150, millor cas). «Entre cinc i deu» és defensable de mitjana però no cobreix els extrems. **Recomanació:** o bé «fins a un ordre de magnitud», o bé acotar la franja amb les xifres exactes de la Taula de ch6 per blindar la frase.

### N-9 · Doble espai dins de taula (cosmètic en font)
**Fitxer:** `6_validacio.tex:44–45` (alineació de columnes amb espais múltiples). Inofensiu en el PDF (és dins d'un entorn tabular), però es marca per completesa de l'auditoria d'edició.

### N-10 · `managment` (typo reproduït de l'API)
**Fitxers:** `5_implementacio.tex:195,208` · `3_requisits.tex` (grants). El *grant* `managment` és la cadena real del backend (errata d'origen), per la qual cosa **no s'ha de "corregir"** al codi; però apareix en prosa/taules sense advertència. **Recomanació:** afegir un breu incís («el valor `managment`, escrit així al backend per compatibilitat») perquè no sembli una errata de la memòria.

---

## 4. Observacions sobre coherència de veu i registre (sense acció obligatòria)

- El cos de la memòria manté de forma **molt consistent** la veu impersonal/passiva («s'ha dissenyat», «s'ha optat»). El canvi a **primera persona del singular** a §*Conclusions personals* (`8_conclusions.tex:50–60`) és **correcte i convencional** per a aquesta secció.
- Única microbarreja: a `8_conclusions.tex:58` apareix «**Les vam gestionar**» (1a persona del plural) enmig del relat en 1a del singular; queda justificat per «l'equip de Lanaccess i jo», però es podria homogeneïtzar si es vol un registre estrictament uniforme.
- Densitat alta del verb «realitzar» (≈29 ocurrències, 13 al Capítol 5). No és incorrecte, però alternar amb «fer/efectuar/dur a terme» milloraria la fluïdesa («realitzar una petició» → «fer una petició»).

---

## 5. Resum prioritzat per a l'entrega

| # | Severitat | Tema | Acció mínima |
|---|-----------|------|--------------|
| C-1 | **Crític** | Xifres `expiresIn` del Config Lock (30/60/120) incoherents | Fixar taula única i propagar a ch1/ch3/ch5 |
| C-2 | **Crític** | Ch5 no explica la detecció/commutació H.265→H.264 promesa a ch1 | Afegir la secció o suavitzar la promesa |
| C-3 | **Crític** | WHEP (HTTP) vs senyalització WebSocket per a `/stream/` | Decidir mecanisme i unificar ch3/ch4/ch5 |
| M-1 | Moderat | VFS «integrat amb FS2» (ch1) vs «independents» (ch6) | Reescriure ch1:80 segons ch4/ch6 |
| M-2 | Moderat | Safari reivindicat fora de RNF-07/UAT | Afegir a abast o retirar de Conclusions |
| M-3 | Moderat | Keep-alive 90 s (RNF) vs 120 s (impl) | Unificar xifra |
| M-4 | Moderat | Càmeres 150/151/152 | Fixar límit i índexs coherents |
| M-5 | Moderat | WHEP/SDP/ICE sense desplegar a ch3 | Desplegar a la 1a aparició / taula d'acrònims |
| M-6 | Moderat | `ttyd` (shell web) vs discurs de seguretat | Justificar+protegir o retirar |
| M-7 | Moderat | `la-db-engine`: gRPC vs HTTPS del navegador | Aclarir interfícies |
| N-1…N-10 | Detall | Espai dur `\cite`, taules de rutes, «degut a», «a nivell de», «microcontrolador», DTLS/SRTP, etc. | Correccions d'edició menors |

**Conclusió de l'avaluador:** la memòria està molt per sobre del llindar d'aprovat i transmet un treball d'enginyeria real i ben gestionat. Els tres punts crítics **no afecten la validesa global del projecte**, però són exactament el tipus d'incoherència numèrica/tècnica que un tribunal explota per posar el candidat en dificultats. Resoldre C-1, C-2 i C-3 abans del lliurament definitiu eliminaria els vectors d'atac més evidents de la defensa.
