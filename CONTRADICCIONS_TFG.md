# CONTRADICCIONS_TFG.md
## Informe d'Auditoria de Qualitat i Coherència — Web Core View TFG

**Rol:** Auditor de Qualitat Tècnica (QA) i Revisor Acadèmic  
**Abast:** Tots els fitxers `.tex` de la carpeta `capitols/` (9 fitxers + `annexos.tex`)  
**Data d'anàlisi:** 2026-06-22  
**Metodologia:** Lectura íntegra i creuament sistemàtic entre capítols. Cap fitxer ha estat modificat.

---

## BLOC 1 — Contradiccions Tècniques

> Un component, flux o integració s'explica d'una manera en un capítol i de manera diferent o incompatible en un altre.

---

### CT-01 · Mecanisme d'autenticació: JWT vs Cookies HttpOnly/Secure

**Concepte analitzat:** Sistema de gestió de sessió del backend.

**Capítol 1** (`1_introduccio.tex`, línies 104–109 i 133):
> *"JWT: La gestió des del frontend implica emmagatzemar el token a LocalStorage o SessionStorage, fet que els exposa a atacs XSS. S'ha optat per les cookies HttpOnly/Secure gestionades per NGINX perquè eliminen el vector d'atac XSS sobre les credencials."*

**Capítol 4** (`4_arquitectura.tex`, línia 144):
> *"A més, permet que el **token JWT** generat per l'autenticació sigui vàlid per a tots els microserveis sota un mateix domini de seguretat, simplificant la gestió de capçaleres Authorization i evitant problemes de CORS."*

**Per què és una contradicció:**  
El Capítol 1 (i els Capítols 3, 5 i 8 de forma consistent) justifica explícitament l'elecció de cookies HttpOnly/Secure **en lloc de JWT**, argumentant que JWT emmagatzemat al client és un vector d'atac XSS. No obstant, el Capítol 4 —dins la mateixa secció "Seguretat Defensiva" que descriu les cookies— menciona "token JWT generat per l'autenticació" com si fos el mecanisme actiu. Aquesta afirmació és incompatible amb el disseny triat i pot induir a confusió sobre quin sistema s'utilitza realment. Sembla un residu d'una versió anterior del document on es valorava JWT.

---

### CT-02 · Format de l'esquema JSON: diccionari pla vs. estructura jeràrquica niada

**Concepte analitzat:** Format de l'esquema de metadades enviat pel backend al frontend per al motor dinàmic.

**Capítol 1** (`1_introduccio.tex`, línia 93) i **Annex A** (`annexos.tex`):
> *"El backend envia al frontend un **diccionari pla de claus en notació de punts** (p. ex., `camera.{id}.stream.codec`) on cada valor porta metadades amb prefix underscore."*

L'Annex mostra el format pla explícitament:
```json
{
  "camera.{id}.name":          { "_type": "string", "_max": 64, ... },
  "camera.{id}.stream.codec":  { "_type": "list", "_allowedValues": [...], ... },
  "camera.{id}.stream.fps":    { "_type": "integer", "_min": 1, "_max": 60, ... }
}
```

**Capítol 5** (`5_implementacio.tex`, Llistat `lst:schema_json`, línies 463–514):
L'exemple de codi real del backend mostra una **estructura JSON jeràrquica niada**, no un diccionari pla:
```json
{
  "config.video.cam": {
    "{camId}": {
      "_rule": { "indexFrom": 0, "indexTo": 151, "padding": 3 },
      "base": {
        "disabled":  { "_type": "boolean", "_default": "false" },
        "ipcamera": {
          "class":   { "_type": "list", "_allowedValues": ["VIVOTEK","ONVIF",...] },
          "connection": {
            "address": { "_type": "ipaddr", "_default": "0.0.0.0" },
            "port": { "config": { "_type": "numeric", "_minValue": 1, "_maxValue": 65535 } }
          },
          "account": { "password": { "_type": "password", "_maxLength": 24 } }
        }
      }
    }
  }
}
```

**Per què és una contradicció:**  
Són dos formats estructuralment incompatibles. En el format pla (Capítol 1 i Annex), la clau és una cadena en notació de punts (`camera.{id}.stream.codec`) i el valor directament les metadades. En el format real (Capítol 5), la clau arrel és un node (`config.video.cam`) i les propietats estan niades en sub-objectes. Un lector que llegeixi el Capítol 1 o l'Annex tindrà una comprensió incorrecta de com funciona el motor schema-driven. A més, el Capítol 4 descriu que el frontend fa una petició per a "nodes de l'arbre de configuració" (ex: `config.network`), cosa que és consistent amb l'estructura niada (Capítol 5) però no amb el diccionari pla (Capítol 1/Annex).

---

### CT-03 · Noms dels camps numèrics de l'esquema: `_min`/`_max` vs. `_minValue`/`_maxValue`

**Concepte analitzat:** Nom de les metadades de rang numèric a l'esquema JSON.

**Capítol 1** (`1_introduccio.tex`, línia 93) i **Annex A** (`annexos.tex`):
> *"on cada valor porta metadades amb prefix underscore: `_type`, `_allowedValues`, `_min`, `_max`, `_default` i `_description`."*

L'Annex confirma: `"_min": 1, "_max": 60` per al camp de FPS.

**Capítol 5** (`5_implementacio.tex`, línies 521–527 i exemple `lst:schema_json`):
El text de la implementació diu:
> *"Renderitza un camp numèric amb les restriccions `_minValue` i `_maxValue` de l'esquema aplicades com a atributs HTML natius."*

I el codi del template HTML confirma que el sistema gestiona **dues variants simultànies**:
```html
[min]="def._min !== undefined ? def._min : def._minValue"
[max]="def._max !== undefined ? def._max : def._maxValue"
```
A més, l'exemple real del schema (Capítol 5) usa `"_minValue": 1, "_maxValue": 65535` (no `_min`/`_max`).

**Per què és una contradicció:**  
El Capítol 1 i l'Annex declaren un únic estàndard de camps (`_min`/`_max`), però la implementació real del Capítol 5 usa noms de camps diferents (`_minValue`/`_maxValue`) i el codi evidencia que existeixen **dues convencions de noms simultànies** al backend. L'Annex és, per tant, una simplificació inexacta del format real. El fet que el codi del frontend hagi de gestionar ambdues variants suggereix una inconsistència no resolta a nivell d'API del backend.

---

### CT-04 · Canal WebSocket de difusió d'alarmes: `la-core-alarm` vs. `la-core-notifier`

**Concepte analitzat:** Quin microservei WebSocket s'encarrega d'enviar les notificacions d'alarma als clients connectats.

**Capítol 3** (`3_requisits.tex`, línia 159) i **Capítol 4** (`4_arquitectura.tex`, línia 15):
L'arquitectura estableix clarament dos canals diferenciats:
- `la-core-notifier` (`/ws/la-core-notifier/`): notificacions globals del sistema.
- `la-core-alarm` (`/ws/la-core-alarm/`): **especialitzat en alarmes**, actiu "exclusivament a la pàgina d'alarmes per a esdeveniments de detecció en temps real".

**Capítol 5** (`5_implementacio.tex`, línia 983):
Al final de la descripció del cicle de vida de les alarmes, el text diu:
> *"el mòdul del backend llança l'esdeveniment unificat a la cua `global_emission_queue`, des d'on s'extreu per al seu enviament a través del WebSocket d'alta velocitat de notificacions **(la-core-notifier)**."*

**Per què és una contradicció:**  
La totalitat del disseny arquitectural (Capítols 3, 4) i la descripció del microservei `la-core-alarm` (un servei independent amb el seu propi fil d'emissió descrit al Capítol 5) estableix que les alarmes es transmeten via `la-core-alarm`. La menció de `la-core-notifier` en el cicle de vida de les alarmes és una contradicció directa amb l'arquitectura. Addicionalment, el Capítol 5, línia 222, mostra que `la-core-notifier` sí que inclou el tòpic "alarms" a la subscripció, però per als comptadors del badge (errors globals), no per a la gestió detallada d'alarmes. La confusió entre els dos canals és un risc de coherència significatiu.

---

### CT-05 · Ruta NGINX per a la llista de reproducció HLS de gravacions

**Concepte analitzat:** URL que el frontend usa per demanar el fitxer de manifest HLS de gravacions.

**Capítol 5** (`5_implementacio.tex`, línia 815, text en prosa):
> *"el client demana la llista de reproducció realitzant una crida HTTP GET cap a la ruta **`/hls-play.m3u8?token=...&start=...&end=...`**"*

**Capítol 5** (`5_implementacio.tex`, Llistat `lst:hls_load_code`, línia 870, codi real):
```javascript
const hlsUrl = `/video-engine/hls-play.m3u8?token=${token}&start=${start}&end=${end}`;
```

**Per què és una contradicció:**  
El text en prosa descriu la ruta `/hls-play.m3u8`, però el codi real de l'aplicació usa `/video-engine/hls-play.m3u8`. Són rutes completament diferents: sense el prefix `/video-engine/`, la petició no es redirigeix a `la-video-engine` (port 60890) a través de NGINX sinó que probablement resultaria en un error 404. La ruta correcta és la del codi. El text en prosa és inexacte i podria confondre un lector que intentés reproduir o entendre el flux de gravacions.

---

### CT-06 · Capacitat màxima de càmeres: 150 vs. 152 (índexs 0–151)

**Concepte analitzat:** Nombre màxim de càmeres IP suportades pel sistema.

**Capítol 4** (`4_arquitectura.tex`, línia 239):
> *"una mateixa versió de la interfície web sigui capaç de gestionar tant un gravador compacte de 4 càmeres com una unitat de rack de **150 càmeres**"*

**Capítol 5** (`5_implementacio.tex`, Llistat `lst:schema_json`, línia 466):
L'esquema JSON real del backend defineix la col·lecció de càmeres amb la regla:
```json
"_rule": { "indexFrom": 0, "indexTo": 151, "padding": 3, "fixed": true }
```
Índexs de 0 a 151 inclusivament = **152 instàncies**.

**Per què és una contradicció:**  
El Capítol 4 afirma que el màxim és 150 càmeres, però l'esquema real del backend indica que el rang d'índexs arriba fins al 151 (152 entrades). Si tots els índexs són vàlids, la capacitat és 152 i no 150. Si els índexs 0 i/o 151 estan reservats per a ús intern, això no s'explica en cap capítol. Qualsevol d'aquests casos implica una discrepància entre la documentació i la implementació real.

---

## BLOC 2 — Inconsistències de Gestió i Narrativa

> Les justificacions, l'abast temporal, les fites o les decisions de scoping descrites al capítol de gestió no quadren amb la realitat explicada a la implementació o amb les conclusions finals.

---

### IG-01 · Declaració del "100% de desenvolupament" inclou mòduls no implementats nativament

**Concepte analitzat:** L'estat final de completesa del projecte declarat al Capítol de Gestió.

**Capítol 2** (`2_gestio.tex`, línia 146, nota de tancament):
> *"A data de lliurament final del projecte (23 de juny de 2026), **tots els mòduls han assolit el 100% de desenvolupament**, confirmant l'acompliment integral de l'abast previst."*

**Capítol 1** (`1_introduccio.tex`, línia 158–159) i **Capítol 5** (`5_implementacio.tex`, línia 641–644):
El Capítol 1 lista la reimplementació Angular nativa dels **Wizards** com a **objectiu desitjable** (no obligatori) i reconeix que "la solució actual adapta visualment l'eina legacy via CSS". El Capítol 5 confirma que **no s'ha reimplementat en Angular**:
> *"Afrontar una reimplementació completa en Angular de la totalitat de variants existents hauria estat inabordable dins l'abast del projecte. La decisió de disseny adoptada va ser reutilitzar la lògica ja provada de l'eina legacy (core-legacy) i adaptar-ne únicament la capa de presentació."*

De la mateixa manera, el Capítol 1 (línia 159) diu que el mòdul de **Reports** "s'està implementant **posteriorment a l'entrega** com a extensió del producte", i el Capítol 8 el lista com a "treball futur" de la versió 2.0.

**Per què és una inconsistència:**  
La declaració del 100% al Capítol 2 no matisa que inclou únicament els mòduls de l'abast obligatori. Un lector que llegeixi el Capítol 2 aïlladament pot concloure que els Wizards han estat reimplementats en Angular i que el mòdul de Reports és funcional, quan la realitat descrita als Capítols 1, 5 i 8 és la contrària. La nota de tancament hauria de precisar "100% dels mòduls de l'abast obligatori".

---

### IG-02 · Durada del projecte: "5 mesos" (amortització) vs. "curs acadèmic" (introducció i conclusions)

**Concepte analitzat:** La durada temporal real del projecte, rellevant per a la coherència del càlcul pressupostari.

**Capítol 2** (`2_gestio.tex`, línia 169, secció d'amortització):
> *"El càlcul de l'amortització del maquinari s'ha realitzat considerant la part proporcional dels actius físics utilitzats durant la durada del projecte (**5 mesos**)"*

**Capítol 1** (`1_introduccio.tex`, línia 11) i **Capítol 8** (`8_conclusions.tex`, línia 8):
> *"El projecte s'ha desenvolupat durant el **curs acadèmic 2025–2026**"*
> *"585 hores distribuïdes al llarg d'un **curs acadèmic**"*

**Per què és una inconsistència:**  
El curs acadèmic 2025-2026 abarca aproximadament 9-10 mesos, però el Capítol 2 fixa 5 mesos com a durada de referència per als càlculs d'amortització. Si el projecte realment va durar 9-10 mesos, el cost d'amortització del maquinari hauria de ser entre 90€ i 120€ superior al calculat (usant el mateix mètode lineal: 5 mesos → 210€; 9 mesos → 378€). La inconsistència afecta la credibilitat del pressupost. Si la durada efectiva de treball intensiu va ser de 5 mesos, caldria justificar-ho explícitament per evitar la contradicció amb les referències al "curs acadèmic".

---

### IG-03 · Cobertura de proves del 85% declarada en un sistema schema-driven sense staging real

**Concepte analitzat:** La cobertura de codi declarada al Capítol de Validació en relació als obstacles reconeguts al Capítol d'Introducció.

**Capítol 6** (`6_validacio.tex`, línia 18):
> *"S'ha assolit una cobertura de codi (code coverage) global superior al **85%** en els fitxers de serveis."*

**Capítol 1** (`1_introduccio.tex`, línia 179):
> *"L'absència d'un entorn de staging permanent **obliga a usar mocks per a les capes de vídeo i emmagatzematge**, limitant la cobertura dels tests automàtics."*

**Per què és una inconsistència:**  
El Capítol 1 reconeix explícitament que les proves automàtiques no poden cobrir els fluxos de vídeo real ni l'emmagatzematge per absència d'entorn de staging. El Capítol 6, tanmateix, declara un 85% de cobertura "en els fitxers de serveis" sense precisar que les parts crítiques del sistema (streaming WebRTC, HLS, escriptura a FS2) queden excloses d'aquesta mesura per estar cobertes per mocks. La manca de matisació pot donar una impressió de cobertura més àmplia del que realment és.

---

## BLOC 3 — Terminologia Inestable

> Conceptes clau que s'anomenen de diverses maneres al llarg del text (termes en anglès i català alternats, acrònims no unificats).

---

### TI-01 · Nom del microservei de vídeo: `video-engine` vs. `la-video-engine`

| Fitxer | Terme usat |
|--------|------------|
| `1_introduccio.tex` (línia 140) | **`video-engine`** (sense prefix "la-") |
| `4_arquitectura.tex` (passim) | **`la-video-engine`** |
| `5_implementacio.tex` (passim) | **`la-video-engine`** |
| `6_validacio.tex` (línia 79) | **`la-core-legacy`** (en el test de càrrega sobre el servei correcte) |

**Risc:** El Capítol 1 (Introducció) usa `video-engine` en la llista de microserveis integrats, però la resta del document (incloent les configuracions NGINX, diagrames i descripció d'arquitectura) l'anomena `la-video-engine`. Qualsevol referència externa o cita del Capítol 1 aïlladament pot induir a buscar un servei anomenat `video-engine` que no existeix amb aquest nom.

---

### TI-02 · Rol d'usuari privilegiat: `Administrador tècnic` vs. `Administrador de Sistema`

| Fitxer | Terme usat |
|--------|------------|
| `1_introduccio.tex` (línia 200) | **Administrador tècnic** |
| `3_requisits.tex` (línia 16) | **Administrador de Sistema** |
| `3_requisits.tex` (taules UC-00, UC-01) | **Administrador** (abreviat) |

**Risc:** El mateix rol (usuari amb privilegis totals sobre el NVR: configuració, firmware, llicències, usuaris) s'anomena de tres maneres diferents en tres seccions del mateix document. A la Taula~\ref{tab:comparativa_mercat} i als Casos d'Ús, l'abreviatura "Administrador" no precisa si és "tècnic" o "de Sistema". Aquesta variació pot generar confusió en seccions posteriors quan es citen permisos o rols.

---

### TI-03 · Actor "Tècnic" en les User Stories no definit als actors del sistema

| Fitxer | Referència |
|--------|------------|
| `3_requisits.tex` (línia 12) | Defineix **3 actors**: Administrador de Sistema, Operador, Videogravador (System Actor) |
| `3_requisits.tex` (línies 91–103) | US-09, US-10, US-12, US-13 usent **"Com a Tècnic"** com a actor principal |

**Risc:** El rol "Tècnic" apareix en 4 User Stories però no figura en la secció formal "Identificació d'actors". Un lector del Capítol 3 no pot determinar si "Tècnic" és sinònim d'"Administrador de Sistema", d'"Operador", o un quart rol no documentat. Afecta la traçabilitat dels requisits.

---

### TI-04 · Mecanisme de bloqueig de configuració: `bloqueig distribuït` vs. `Pessimistic Locking`

| Fitxer | Terme usat |
|--------|------------|
| `1_introduccio.tex` (línia 132) | **bloqueig distribuït** |
| `3_requisits.tex` (línia 167) | **bloqueig pessimista (Pessimistic Locking)** |
| `5_implementacio.tex` (línia 557) | **Pessimistic Locking** |
| `4_arquitectura.tex` (línia 193) | **lock de configuració** |

**Risc:** El terme "bloqueig distribuït" del Capítol 1 suggereix un mecanisme de coordinació entre múltiples nodes (distribució geogràfica o lògica), quan en realitat es tracta d'un bloqueig pessimista centralitzat en el `la-core-legacy` d'un únic NVR. La resta de capítols usa "Pessimistic Locking" que és el terme tècnic correcte per a aquest patró. El Capítol 1 usa un terme que pot induir a una interpretació arquitectural incorrecta.

---

### TI-05 · Metadades de l'esquema: `_min`/`_max` (Annex) vs. `_minValue`/`_maxValue` (Capítol 5)

| Fitxer | Camp usat per a rangs numèrics |
|--------|-------------------------------|
| `1_introduccio.tex` (línia 93) | `_min`, `_max` |
| `annexos.tex` (exemple FPS) | `"_min": 1, "_max": 60` |
| `5_implementacio.tex` (línia 523, text) | `_minValue`, `_maxValue` |
| `5_implementacio.tex` (lst:schema_json) | `"_minValue": 1, "_maxValue": 65535` |
| `5_implementacio.tex` (template HTML) | Gestiona **ambdós**: `def._min \|\| def._minValue` |

**Risc:** Hi ha dos estàndards de noms per al mateix concepte (límit inferior/superior d'un camp numèric), l'Annex usa un format i el codi real usa l'altre. El template HTML confirma que el backend és inconsistent en l'ús d'un o altre. La documentació acadèmica hauria d'escollir un únic estàndard i reflectir-lo consistentment, o bé explicar explícitament per quin motiu coexisteixen dues convencions.

---

### TI-06 · Motor de configuració dinàmica: terminologia múltiple per al mateix component

Al llarg del document, el mateix component arquitectural central rep els noms següents:

| Fitxer | Terme usat |
|--------|------------|
| `1_introduccio.tex` | *arquitectura Schema-driven*, *Motor Dinàmic* |
| `2_gestio.tex` | *motor de configuració dinàmic*, *motor schema-driven* |
| `3_requisits.tex` | *schema-driven design*, *RF-3: Motor de Configuració Dinàmica* |
| `4_arquitectura.tex` | *motor schema-driven*, *ConfigInputComponent* |
| `5_implementacio.tex` | *Motor Dinàmic del Frontend*, *intèrpret* |
| `7_integracio_coneixements.tex` | *motor d'interpretació dinàmica schema-driven* |
| `8_conclusions.tex` | *motor de configuració dinàmica* |

**Risc:** Malgrat que el context sempre permet deduir a quin component es refereix, l'absència d'un nom canònic únic dificulta la cerca i la indexació del document, i pot confondre un lector extern que intenti entendre l'arquitectura llegint capítols de forma no seqüencial. La convenció recomanable seria fixar un terme (ex.: *Motor Schema-driven*) i usar-lo de forma consistent.

---

### TI-07 · Descripció del xifratge WebRTC: DTLS i SRTP usats de forma imprecisa

| Fitxer | Descripció |
|--------|------------|
| `1_introduccio.tex` (línia 70) | *"transport sobre UDP sota **xifratge DTLS**"* |
| `3_requisits.tex` (línia 290) | *"**DTLS** per al canal de control i **SRTP** per a la seguretat en els paquets multimèdia"* (correcte) |
| `4_arquitectura.tex` (línia 16) | *"transport **SRTP/UDP** negociat per ICE"* |

**Risc:** El Capítol 1 atribueix el xifratge del transport de vídeo a DTLS. Tècnicament, DTLS s'usa per a la negociació de claus (handshake), però el transport real dels paquets de vídeo utilitza **SRTP** (sobre UDP). El Capítol 3 fa la distinció correcta; els Capítols 1 i 4 la simplifiquen de forma imprecisa però en sentits oposats. Aquesta inconsistència és rellevant en un document acadèmic que inclou una secció de seguretat i compliment de l'OWASP.

---

### TI-08 · HLS etiquetat com a "Fallback" a la taula de latències, però és el protocol primari per a gravacions

| Fitxer | Referència |
|--------|------------|
| `6_validacio.tex` (Taula `tab:latencies`) | Fila: *"**HLS (Fallback)**"* amb latència 2.000–5.000 ms |
| `5_implementacio.tex` (passim, Secció 5.8) | HLS és el **protocol primari i únic** per a la reproducció de gravacions, no un fallback |

**Risc:** La Taula de latències del Capítol 6 etiqueta HLS únicament com a "Fallback" (per al vídeo en directe), donant la impressió que HLS sempre opera amb latències de 2-5 segons. Tanmateix, per a la reproducció de gravacions (cas d'ús central del sistema, descrit àmpliament al Capítol 5), HLS és el protocol **primari** i la latència no és un factor crític. L'etiqueta "HLS (Fallback)" és correcta en el context de vídeo en directe, però sense matisació pot desvalorar injustament la tecnologia HLS davant d'un lector que l'avaluï per al cas de gravacions.

---

## Resum Executiu de Troballes

| ID | Tipus | Gravetat* | Fitxers afectats |
|----|-------|-----------|-----------------|
| CT-01 | Tècnica | **Alta** | `1_introduccio.tex`, `4_arquitectura.tex` |
| CT-02 | Tècnica | **Alta** | `1_introduccio.tex`, `5_implementacio.tex`, `annexos.tex` |
| CT-03 | Tècnica | **Mitjana** | `1_introduccio.tex`, `5_implementacio.tex`, `annexos.tex` |
| CT-04 | Tècnica | **Alta** | `3_requisits.tex`, `4_arquitectura.tex`, `5_implementacio.tex` |
| CT-05 | Tècnica | **Mitjana** | `5_implementacio.tex` (text vs. codi) |
| CT-06 | Tècnica | **Baixa** | `4_arquitectura.tex`, `5_implementacio.tex` |
| IG-01 | Gestió | **Mitjana** | `2_gestio.tex`, `1_introduccio.tex`, `5_implementacio.tex`, `8_conclusions.tex` |
| IG-02 | Gestió | **Mitjana** | `2_gestio.tex`, `1_introduccio.tex`, `8_conclusions.tex` |
| IG-03 | Gestió | **Baixa** | `6_validacio.tex`, `1_introduccio.tex` |
| TI-01 | Terminologia | **Baixa** | `1_introduccio.tex` vs. tots els altres |
| TI-02 | Terminologia | **Mitjana** | `1_introduccio.tex`, `3_requisits.tex` |
| TI-03 | Terminologia | **Mitjana** | `3_requisits.tex` |
| TI-04 | Terminologia | **Baixa** | `1_introduccio.tex`, `3_requisits.tex`, `5_implementacio.tex` |
| TI-05 | Terminologia | **Mitjana** | `1_introduccio.tex`, `5_implementacio.tex`, `annexos.tex` |
| TI-06 | Terminologia | **Baixa** | Tots els capítols |
| TI-07 | Terminologia | **Baixa** | `1_introduccio.tex`, `3_requisits.tex`, `4_arquitectura.tex` |
| TI-08 | Terminologia | **Baixa** | `6_validacio.tex`, `5_implementacio.tex` |

*Gravetat: **Alta** = pot induir a una comprensió incorrecta del disseny tècnic; **Mitjana** = inconsistència visible que requereix atenció; **Baixa** = imprecisió estilística o menor.

---

### Prioritat de Correcció Recomanada

1. **[URGENT] CT-01**: Eliminar la menció a "token JWT" del Capítol 4 (Seguretat Defensiva), ja que contradiu directament l'arquitectura de sessions implementada.
2. **[URGENT] CT-04**: Corregir la referència a `la-core-notifier` en el cicle de vida de les alarmes (Capítol 5, línia 983) → hauria de ser `la-core-alarm`.
3. **[ALTA] CT-02**: Unificar la descripció del format de l'esquema JSON (pla vs. jeràrquic) entre el Capítol 1, l'Annex A i el Capítol 5, o afegir una nota que expliqui que l'Annex és una simplificació del format real.
4. **[ALTA] CT-05**: Corregir la ruta HLS al text del Capítol 5 (línia 815) de `/hls-play.m3u8` a `/video-engine/hls-play.m3u8`.
5. **[MITJANA] TI-02 i TI-03**: Unificar el nom del rol d'Administrador i definir formalment el rol "Tècnic" o mapar-lo als actors existents.
6. **[MITJANA] IG-01**: Matisar la nota de tancament del Capítol 2 per precisar que el 100% es refereix als mòduls de l'abast obligatori.

---

*Document generat per auditoria de qualitat. Cap fitxer `.tex` ha estat modificat durant aquest procés.*
