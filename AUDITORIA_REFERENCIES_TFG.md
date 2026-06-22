# AUDITORIA DE REFERÈNCIES CREUADES — TFG Web Core View
**Data:** 2026-06-22  
**Metodologia:** Inspecció estàtica dels fitxers `capitols/*.tex`. Anàlisi de: patrons de referència espacial, ús del delimitador de no-trencament `~`, i labels definits però mai citats amb `\ref{}`.  
**IMPORTANT:** Document de diagnòstic. **Prohibit modificar fitxers font fins a revisió manual.**

---

## RESUM EXECUTIU

| Escenari | Instàncies | Prioritat |
|---|---|---|
| 1 — Referències espacials/col·loquials | 0 trobades + 1 cas especial | Baixa |
| 2 — `\ref{}` sense `~` fora de parèntesis | **17** instàncies en 5 capítols | **Alta** |
| 2b — `\ref{}` sense `~` dins parèntesis | 24 instàncies en 3 capítols | Mitjana |
| 3a — Labels `lst:` òrfens (mai citats) | **23** labels de llistats | Alta |
| 3b — Labels `fig:` òrfens | 4 figures no citades en prosa | Mitjana |
| 3c — Labels `tab:` no cross-referenciats | 17 taules sistemàtiques | Baixa |
| 3d — Labels `sec:/ch:` no citats | 11 seccions/capítols | Molt baixa |

---

## ESCENARI 1: REFERÈNCIES ESPACIALS/COL·LOQUIALS

**Resultat:** Cap expressió espacial del tipus "el llistat de sota", "la taula anterior" o "la figura que segueix" ha estat localitzada en cap dels 9 fitxers `.tex` auditats. La prosa utilitza sistemàticament el mecanisme `\ref{}` o la forma parentètica `(Figura X)`.

### Cas especial — Referència a secció sense tipus explícit
**Fitxer:** `capitols/1_introduccio.tex`, línia 165  
**Fragment original:**
```
Tota la funcionalitat ha de ser operable des d'un navegador modern, sense cap
extensió externa ni infraestructura de servidor addicional més enllà del mateix
gravador NXT (vegeu \ref{sec:solucioproposada}).
```
**Problema:** La comanda `\ref{sec:solucioproposada}` s'usa amb el verb "vegeu" però sense identificar explícitament el tipus d'element ni aplicar el `~`. LaTeX resoldrà el número de secció, però el lector no sap si s'apunta a una secció, pàgina o figura. Tampoc hi ha tilde.  
**Correcció proposada:**
```
(vegeu la Secció~\ref{sec:solucioproposada})
```

---

## ESCENARI 2: ÚS INCORRECTE DEL DELIMITADOR DE SINTAXI `~`

La convenció acadèmica de LaTeX exigeix un espai de no-trencament (`~`) entre la paraula descriptiva i `\ref{}` per evitar que el número de referència quedi separat a l'inici d'una línia nova. La forma correcta és sempre `Figura~\ref{...}`, `Taula~\ref{...}`, `Llistat~\ref{...}`, `Capítol~\ref{...}`.

### 2A — Instàncies FORA de parèntesis (Alta prioritat)

Aquestes instàncies estan en mig de la prosa i presenten el major risc tipogràfic.

---

**Capítol 2** (`capitols/2_gestio.tex`)

| Línia | Fragment original | Correcció |
|---|---|---|
| 65 | `La Taula \ref{tab:estimacio_hores} resumeix` | `La Taula~\ref{tab:estimacio_hores} resumeix` |
| 104 | `tal com es recull a la Taula \ref{tab:matriu_riscos}.` | `tal com es recull a la Taula~\ref{tab:matriu_riscos}.` |

---

**Capítol 4** (`capitols/4_arquitectura.tex`)

| Línia | Fragment original | Correcció |
|---|---|---|
| 66 | `el diagrama de la Figura \ref{fig:arquitectura_serveis}, el` | `el diagrama de la Figura~\ref{fig:arquitectura_serveis}, el` |
| 99 | `a la Taula \ref{tab:matriu_comunicacio} es detallen` | `a la Taula~\ref{tab:matriu_comunicacio} es detallen` |
| 127 | `El Llistat \ref{lst:nginx_routing} mostra els blocs` | `El Llistat~\ref{lst:nginx_routing} mostra els blocs` |
| 244 | `La Figura \ref{fig:angular_components} representa les tres` | `La Figura~\ref{fig:angular_components} representa les tres` |
| 251 | `a la Figura \ref{fig:angular_modules}.` | `a la Figura~\ref{fig:angular_modules}.` |
| 262 | `s'observa a la Figura \ref{fig:angular_modules}, el punt` | `s'observa a la Figura~\ref{fig:angular_modules}, el punt` |

---

**Capítol 5** (`capitols/5_implementacio.tex`)

| Línia | Fragment original | Correcció |
|---|---|---|
| 461 | `La Figura \ref{fig:config_create_user} il·lustra` | `La Figura~\ref{fig:config_create_user} il·lustra` |
| 471 | `El Llistat \ref{lst:schema_json} mostra un fragment` | `El Llistat~\ref{lst:schema_json} mostra un fragment` |
| 716 | `el cicle d'activitat descrit a la Figura \ref{fig:activity_video_connection}.` | `a la Figura~\ref{fig:activity_video_connection}.` |

---

**Capítol 6** (`capitols/6_validacio.tex`)

| Línia | Fragment original | Correcció |
|---|---|---|
| 35 | `Com es pot observar a la Taula \ref{tab:latencies}, el` | `Com es pot observar a la Taula~\ref{tab:latencies}, el` |

---

**Capítol 7** (`capitols/7_integracio_coneixements.tex`)

| Línia | Fragment original | Correcció |
|---|---|---|
| 16 | `casos d'ús (Capítol \ref{ch:requisits}) han servit` | `(Capítol~\ref{ch:requisits}) han servit` |
| 76 | `i de validació (Capítol \ref{ch:validacio}). Les proves` | `(Capítol~\ref{ch:validacio}). Les proves` |
| 79 | `de requisits (Capítol \ref{ch:requisits}). A través` | `(Capítol~\ref{ch:requisits}). A través` |
| 87 | `en diferents dimensions (Capítol \ref{ch:gestio}):` | `(Capítol~\ref{ch:gestio}):` |

**Total 2A: 17 instàncies a corregir.**

---

### 2B — Instàncies DINS de parèntesis (Prioritat Mitjana)

Dins dels parèntesis el risc de trencament de línia és menor, però la convenció `~` s'ha d'aplicar per uniformitat estilística. **24 instàncies** distribuïdes principalment al Capítol 5. La correcció és mecànica i consistent: substituir ` \ref{` per `~\ref{` en totes elles.

**Capítol 5** (`capitols/5_implementacio.tex`) — 20 instàncies:

| Línia | Fragment (simplificat) |
|---|---|
| 63 | `(Llistat \ref{lst:scss_variables})` |
| 120 | `(Figura \ref{fig:login})` |
| 259 | `(Figura \ref{fig:ui_home})` |
| 283 | `(Figura \ref{fig:ui_home_panels})` |
| 295 | `(Figura \ref{fig:ui_home_panels}, esquerra)` |
| 305 | `(Figura \ref{fig:ui_home_panels}, dreta)` |
| 374 | `(Figura \ref{fig:ui_home_panels})` i `(Figura \ref{fig:ui_license})` |
| 386 | `(Figura \ref{fig:ui_errors})` |
| 714 | `(Figura \ref{fig:ui_cameras})` |
| 817 | `(Figura \ref{fig:ui_recordings})` |
| 867 | `(Figura \ref{fig:ui_recordings})` |
| 873 | `(Llistat \ref{lst:hls_load_code})` |
| 936 | `(Llistat \ref{lst:dbus_listen_code})` |
| 1009 | `(Figura \ref{fig:ui_alarms})` |
| 1048 | `(Figura \ref{fig:ui_update})` |
| 1049 | `(Figura \ref{fig:ui_upgrading_flash})` |
| 1058 | `(Figura \ref{fig:ui_upgrading_reboot})` (×2) |
| 1083 | `(Figura \ref{fig:ui_configuration_actions})` |
| 1100 | `(Figura \ref{fig:ui_log})` |
| 1129 | `(Figura \ref{fig:ui_documentation})` |

**Capítol 7** (`capitols/7_integracio_coneixements.tex`) — 4 instàncies ja recollides a la Taula 2A (les de cap. 7 usen `(Capítol \ref{...})` que és la forma parentètica).

---

## ESCENARI 3: LABELS ÒRFENS

Un label és **òrfe** quan apareix definit al document (`\label{...}` o `label={...}` en un lstlisting) però cap comanda `\ref{}` del text el cita mai. Això no és un error de compilació però sí un indicador que l'element visual no està integrat narrativament en el discurs acadèmic.

---

### 3A — Llistats de codi òrfens (23 labels — Alta prioritat)

Aquests labels van ser injectats durant la sessió d'auditoria visual prèvia. La prosa contigua descriu el codi però **mai usa un `\ref{}`** per remetre explícitament al llistat. Si el Capítol 5 és llegit linealment, el lector sí veu el llistat immediatament després del text. No obstant, la norma acadèmica exigeix que la prosa mencioni el llistat per número.

**Preexistent (no afectat per la darrera auditoria):**

| Label | Fitxer | Descripció |
|---|---|---|
| `lst:nginx_security` | `4_arquitectura.tex` | Configuració de seguretat NGINX. La prosa descriu el bloc però cap `\ref{}` l'esmenta explícitament. |

**Nous labels injectats al Capítol 5 (mai citats en prosa):**

**Subsecció: Autenticació i sessió**

| Label | Descripció del llistat |
|---|---|
| `lst:media_query_responsive` | Media Query de disseny responsiu (SCSS) |
| `lst:plainlogin_request` | Petició HTTP de credencials PlainLogin |
| `lst:auth_responses` | Respostes de l'API d'autenticació |
| `lst:whoami_request` | Petició whoami per perfil i permisos |

**Correcció proposada (exemple per `lst:plainlogin_request`):**  
Afegir a la prosa del paràgraf anterior: `com s'il·lustra al Llistat~\ref{lst:plainlogin_request}` o `(vegeu Llistat~\ref{lst:plainlogin_request})`.

---

**Subsecció: Protocol WebSocket i notificacions**

| Label | Descripció del llistat |
|---|---|
| `lst:keepalive_msg` | Missatge Keep-Alive del WebSocket |
| `lst:subscription_msg` | Missatge de subscripció a `la-core-notifier` |
| `lst:errors_notify_msg` | Payload `ERRORS_NOTIFY` |

---

**Subsecció: Configuració schema-driven**

| Label | Descripció del llistat |
|---|---|
| `lst:schema_input_binding` | Vinculació d'input HTML5 a l'esquema |
| `lst:password_requirements_signal` | Signal `passwordRequirements` |
| `lst:config_lock_json` | JSON del bloqueig de configuració (Pessimistic Lock) |
| `lst:save_config_node` | Funció `saveConfigNode` |
| `lst:auth_interceptor` | Interceptor `authInterceptor` |
| `lst:update_ref_after_save` | Actualització de la referència `originalConfigJson` |

---

**Subsecció: Streaming i reproductor de vídeo**

| Label | Descripció del llistat |
|---|---|
| `lst:streaming_strategy_iface` | Interfície `StreamingStrategy` |
| `lst:io_item_model` | Model TypeScript `IoItem` |
| `lst:io_notify_payload` | Payload `IO_NOTIFY` WebSocket |
| `lst:output_activation_req` | Petició d'activació de sortida física |

---

**Subsecció: Alarmes**

| Label | Descripció del llistat |
|---|---|
| `lst:thread_safe_queue` | Cua thread-safe de D-Bus |
| `lst:alarm_prune_trigger` | Disparador de poda automàtica d'alarmes |
| `lst:alarm_read_msg` | Missatge `ALARM_READ` |

---

**Subsecció: Internacionalització (i18n)**

| Label | Descripció del llistat |
|---|---|
| `lst:i18n_en` | Fitxer `en.json` — traduccions anglès |
| `lst:i18n_es` | Fitxer `es.json` — traduccions espanyol |

**Total 3A: 23 labels de llistats sense cap `\ref{}` en tota la prosa.**

---

### 3B — Figures òrfenes (4 labels — Prioritat Mitjana)

Figures que apareixen al document però la prosa no les menciona en cap lloc.

| Label | Fitxer | Línia `\label` | Situació |
|---|---|---|---|
| `fig:seq_login` | `5_implementacio.tex` | 212 | Diagrama de seqüència del flux de login. Es renderitza però la prosa no inclou cap `(Figura~\ref{fig:seq_login})`. |
| `fig:config_network` | `5_implementacio.tex` | 440 | Captura de la pàgina de configuració de xarxa. La prosa descriu el mòdul però no cita la figura. |
| `fig:alarms_data_flow` | `5_implementacio.tex` | 919 | Diagrama de flux de dades del microservei d'alarmes. No citat. |
| `fig:model_conceptual` | `3_requisits.tex` | 415 | Diagrama del model conceptual del domini. Cap `\ref{}` en tota la prosa del Cap. 3. |

**Nota addicional:** `fig:seq_video_streaming` (cap5, L751) té tant el `\label` com l'únic `\ref{}` que el citaria (L745) en línies comentades (`%`). Si s'activa el contingut comentat en una revisió futura, cal descommentar ambdues línies coordinadament.

---

### 3C — Taules no cross-referenciades (17 labels — Prioritat Baixa)

Aquestes taules formen llistes sistemàtiques (casos d'ús i requisits no funcionals) que es presenten de manera autònoma dins de les seves seccions. En LaTeX acadèmic és habitual que els labels existeixin com a ancoratge de navegació PDF sense ser explícitament citats des d'altres seccions. **No requereix correcció urgent,** però es llisten per completitud.

**Capítol 2:**
- `tab:costos_humans` — Taula de costos humans. No citada des de la prosa (la `tab:amortitzacio` sí que és citada).

**Capítol 3 (totes les taules de UC i RNF):**
- `tab:uc_login`, `tab:uc_firmware`, `tab:uc_playback`, `tab:uc_live`, `tab:uc_alarmes`, `tab:uc_config`, `tab:uc_admin` — 7 taules de casos d'ús.
- `tab:rnf01` a `tab:rnf08` — 8 taules de requisits no funcionals.

**Capítol 5:**
- `tab:nginx_routes` — Taula de rutes NGINX. La prosa del Cap. 5 descriu les rutes però referencia `lst:nginx_routing` del Cap. 4, no aquesta taula.

---

### 3D — Seccions i capítols no citats (Prioritat Molt Baixa)

Labels de secció definits però no citats per cap `\ref{}`. Habitual en documents acadèmics on les seccions serveixen com a navegació interna PDF.

| Label | Fitxer |
|---|---|
| `ch:conclusions` | `8_conclusions.tex` — últim capítol, no sol ser citat |
| `ch:integracio` | `7_integracio_coneixements.tex` — no referenciat en cap altra secció |
| `sec:actors` | `3_requisits.tex` |
| `sec:casos_us` | `3_requisits.tex` |
| `sec:rnf` | `3_requisits.tex` |
| `sec:model_conceptual` | `3_requisits.tex` |
| `sec:disseny_extern` | `4_arquitectura.tex` |
| `sec:nginx_config` | `5_implementacio.tex` |
| `sec:amortitzacio` | `2_gestio.tex` |
| `sec:tancament_projecte` | `2_gestio.tex` |
| `sec:estructura` | `1_introduccio.tex` |
| `sec:declaracio_ia` | `7_integracio_coneixements.tex` |

---

## PLA D'ACCIÓ RECOMANAT

### Pas 1 — Correcció mecànica de les tildes (Escenari 2A)
**17 substitucions** en 5 fitxers. Canvi segur i mecànic. Comanda de referència per a cada fitxer:
```
# Verificar instàncies en cada fitxer:
grep -n ' \\ref{' capitols/4_arquitectura.tex
```

### Pas 2 — Integrar narrativament els llistats òrfens prioritaris (Escenari 3A)
Per a cada llistat òrfe, la prosa adjacent ha d'incloure una frase com:  
> `com s'il·lustra al Llistat~\ref{lst:auth_interceptor}`  
o bé en forma parentètica:  
> `(Llistat~\ref{lst:auth_interceptor})`

### Pas 3 — Citar les figures òrfenes (Escenari 3B)
Les 4 figures sense citació cal integrar-les explícitament al paràgraf que les precedeix.

### Pas 4 — Correcció de les tildes dins parèntesis (Escenari 2B)
24 substitucions mecàniques al Cap. 5. Baixa urgència però recomanable per uniformitat.

---

*Auditoria generada automàticament per inspecció estàtica de `capitols/*.tex`.*  
*Compilació de referència: document de 141 pàgines, 0 errors, latexmk 2026-06-22.*
