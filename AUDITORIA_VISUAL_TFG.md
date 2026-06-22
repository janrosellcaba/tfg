# AUDITORIA VISUAL TFG — Maquetació Editorial LaTeX

**Data:** 2026-06-22  
**Auditor:** Claude Sonnet 4.6 (anàlisi automatitzada + verificació manual)  
**Fitxers analitzats:** `capitols/1_introduccio.tex` fins a `capitols/8_conclusions.tex`, `capitols/annexos.tex`, `main.tex`  
**Metodologia:** Anàlisi sintàctica de tots els entorns `table`, `figure` i `lstlisting`

---

## Resum Executiu

| Tipus        | Total | OK  | AVÍS | ERROR CRÍTIC |
|--------------|-------|-----|------|--------------|
| Taules       | 26    | 9   | 17   | 0            |
| Figures      | 33    | 33  | 0    | 0            |
| Llistats     | 28    | 0   | 26   | 2            |
| **TOTAL**    | **87**| **42** | **43** | **2** |

**Punts d'or verificats per element:**

| Punt | Descripció                            | OK  | AVÍS | CRÍTIC |
|------|---------------------------------------|-----|------|--------|
| 1    | `\caption{...}` present i descriptiu  | 85  | 2    | 0      |
| 2    | Atribució de font (`Font: ...`)        | 43  | 44   | 0      |
| 3    | `\label{...}` en posició correcta      | 64  | 23   | 0      |
| 4    | Absència de `_` no escapats al caption | 87  | 0    | 0      |
| 5    | Opcions de posicionament flexibles     | 86  | 1    | 0      |

---

## Llegenda d'Estat

| Símbol | Significat |
|--------|------------|
| ✅ | Compleix el criteri |
| ⚠️ | Avís — incompliment recomanat |
| 🔴 | Error crític — cal corregir abans de lliurar |

---

## Capítol 1 — Introducció (`1_introduccio.tex`)

### TAB-1.1 · `tab:comparativa_mercat` · L34

**Tipus:** `table[htbp]`

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Comparativa de solucions de videovigilància web.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia a partir de la documentació pública dels fabricants \cite{...}` (L50) |
| 3 | Label correcte | ✅ | `\label{tab:comparativa_mercat}` immediatament sota `\caption` |
| 4 | Underscores escapats | ✅ | Cap underscore al caption |
| 5 | Posicionament | ✅ | `[htbp]` |

**Acció correctora:** Cap.

---

## Capítol 2 — Gestió del Projecte (`2_gestio.tex`)

### TAB-2.1 · `tab:estimacio_hores` · L67

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Estimació d'hores i recursos utilitzats a cada tasca.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L90) |
| 3 | Label correcte | ✅ | `\label{tab:estimacio_hores}` immediatament sota `\caption` |
| 4 | Underscores escapats | ✅ | Cap underscore al caption |
| 5 | Posicionament | ✅ | `[htbp]` |

### FIG-2.1 · `fig:gantt` · L94

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Diagrama de Gantt del projecte.` |
| 2 | Font d'autoria | ✅ | Font integrada al caption: `\\\small Font: Elaboració pròpia.` |
| 3 | Label correcte | ✅ | `\label{fig:gantt}` immediatament sota `\caption` |
| 4 | Underscores escapats | ✅ | Cap underscore |
| 5 | Posicionament | ✅ | `[htbp]` |

### TAB-2.2 · `tab:matriu_riscos` · L106

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Matriu de gestió de riscos i plans de contingència.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L122) |
| 3 | Label correcte | ✅ | `\label{tab:matriu_riscos}` sota caption |
| 4 | Underscores escapats | ✅ | Cap underscore |
| 5 | Posicionament | ✅ | `[htbp]` |

### TAB-2.3 · `tab:costos_humans` · L155

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Costos de recursos humans per rol.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L169) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

### TAB-2.4 · `tab:amortitzacio` · L176

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Càlcul de l'amortització del maquinari del projecte.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L191) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

### TAB-2.5 · `tab:desviacions_temporal` · L233

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Comparativa de la planificació temporal prevista enfront del temps real d'execució.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L250) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

### TAB-2.6 · `tab:desviacions_pressupost` · L265

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Comparativa del cost de recursos humans estimat enfront de l'executat real.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L280) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

---

## Capítol 3 — Requisits (`3_requisits.tex`)

### FIG-3.1 · `fig:diagrama_casos_us` · L44

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Diagrama UML de Casos d'Ús de Web Core View.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L49) |
| 3 | Label correcte | ✅ | `\label{fig:diagrama_casos_us}` sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

---

### Taules de Casos d'Ús (UC-00 a UC-06) — L56 a L226

Les set taules d'especificació de casos d'ús segueixen el mateix patró estructural. S'auditen conjuntament per concisió.

| ID | Label | Línia | Cap. | Font | Label OK | `_` | Pos. |
|----|-------|-------|------|------|----------|-----|------|
| TAB-3.1 | `tab:uc_login` | L56 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.2 | `tab:uc_firmware` | L84 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.3 | `tab:uc_playback` | L112 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.4 | `tab:uc_live` | L140 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.5 | `tab:uc_alarmes` | L168 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.6 | `tab:uc_config` | L197 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.7 | `tab:uc_admin` | L226 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |

**Acció correctora (x7):** Afegir atribució de font. Proposta estandarditzada per a totes:
```latex
\par\smallskip
\small Font: Especificació pròpia elaborada amb metodologia de casos d'ús (UML).
```

---

### Taules de Requisits No Funcionals (RNF-01 a RNF-08) — L255 a L363

| ID | Label | Línia | Cap. | Font | Label OK | `_` | Pos. |
|----|-------|-------|------|------|----------|-----|------|
| TAB-3.8 | `tab:rnf01` | L255 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.9 | `tab:rnf02` | L270 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.10 | `tab:rnf03` | L286 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.11 | `tab:rnf04` | L301 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.12 | `tab:rnf05` | L317 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.13 | `tab:rnf06` | L332 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.14 | `tab:rnf07` | L348 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |
| TAB-3.15 | `tab:rnf08` | L363 | ✅ | ⚠️ | ✅ | ✅ | ✅ `[htbp]` |

**Acció correctora (x8):** Afegir atribució de font. Proposta:
```latex
\par\smallskip
\small Font: Elaboració pròpia seguint la plantilla VOLERE de requisits no funcionals.
```

---

### FIG-3.2 · `fig:model_conceptual` · L381

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Model conceptual de les entitats de negoci del sistema NVR (Web Core View).` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L386) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

---

### TAB-3.16 · `tab:llicencies` · L449 — ⚠️ DOS AVISOS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Llicències de les principals dependències de codi obert de \textit{Web Core View}.` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** Només hi ha referència textual al paràgraf anterior, però no al peu de la taula. |
| 3 | Label correcte | ✅ | `\label{tab:llicencies}` sota caption (L464) |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ⚠️ | **`[h!]` RÍGID** — pot generar espais buits en documents amb molt de contingut flotant. |

**Acció correctora:**
1. Canviar `\begin{table}[h!]` → `\begin{table}[htbp]`
2. Afegir sota `\end{tabular}`:
```latex
\par\smallskip
\small Font: Elaboració pròpia a partir de la documentació oficial de les llicències respectives.
```

---

## Capítol 4 — Arquitectura (`4_arquitectura.tex`)

### FIG-4.1 · `fig:flux_navegacio` · L42

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Diagrama de flux de navegació de l'aplicació \textit{Web Core View}.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L47) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

### FIG-4.2 · `fig:arquitectura_serveis` · L74

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Relació i comunicació entre els serveis de l'NVR.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L79) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

---

### TAB-4.1 · `tab:matriu_comunicacio` · L102 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Matriu de comunicació de la plataforma.` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** La taula no té línia `Font:` al peu. |
| 3 | Label correcte | ✅ | `\label{tab:matriu_comunicacio}` sota caption (L105) |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

**Acció correctora:** Afegir entre `\end{tabular}` i `\end{table}`:
```latex
\par\smallskip
\small Font: Elaboració pròpia.
```

---

### LST-4.1 · `lst:nginx_routing` · L127 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Fragment de la configuració NGINX (la-core.conf, repositori mantos-rec): rutes principals de la plataforma.` |
| 2 | Font d'autoria | ⚠️ | **ABSENT** al peu del llistat. El text del paràgraf anterior (L125) en fa referència però no hi ha una línia `Font:` normalitzada. |
| 3 | Label correcte | ✅ | `label={lst:nginx_routing}` a les opcions de l'entorn |
| 4 | Underscores escapats | ✅ | Cap underscore al caption |
| 5 | Posicionament | ✅ | Llistat inline (sense float) |

**Acció correctora:** Afegir immediatament després de `\end{lstlisting}`:
```latex
{\small\noindent Font: Codi de producció, repositori \texttt{mantos-rec} (\texttt{sources/meta-mantos/recipes-httpd/nginx/la-core.conf}).}
```

---

### LST-4.2 · `lst:nginx_security` · L194 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Fragment de la configuració NGINX (la-core.conf): TLS i capçaleres de seguretat HTTP.` |
| 2 | Font d'autoria | ⚠️ | **ABSENT** al peu del llistat. |
| 3 | Label correcte | ✅ | `label={lst:nginx_security}` a les opcions de l'entorn |
| 4 | Underscores escapats | ✅ | Cap underscore al caption |
| 5 | Posicionament | ✅ | `float=!ht` (float activat) |

**Acció correctora:** Ídem LST-4.1.

---

### FIG-4.3 · `fig:angular_components` · L232

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Diagrama de components de l'arquitectura de l'aplicació Angular i la seva integració amb el backend.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L237) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

### FIG-4.4 · `fig:angular_modules` · L250

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Diagrama de mòduls de l'aplicació Angular i relació amb els components compartits i serveis de la capa Core.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L255) |
| 3 | Label correcte | ✅ | Sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[!htbp]` — el `!` és acceptable (forçar la prioritat); no és una opció rígida. |

---

## Capítol 5 — Implementació (`5_implementacio.tex`)

### TAB-5.1 · `tab:nginx_routes` · L35

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Taula de rutes del proxy invers NGINX de l'NVR (\texttt{la-core.conf}).` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia.` (L52) |
| 3 | Label correcte | ✅ | `\label{tab:nginx_routes}` sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

---

### Llistats de Codi — `5_implementacio.tex` (26 elements)

> **Nota metodològica:** Per als entorns `lstlisting`, el criteri de *caption* es verifica a les opcions de l'entorn (`caption={...}`), no com a comanda `\caption{}` independent.

#### LST-5.1 · `lst:scss_variables` · L65

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Fragment del fitxer de definició de variables d'estil SCSS.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ✅ | `label={lst:scss_variables}` a les opcions |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Llistat inline |

---

#### LST-5.2 · **SENSE LABEL** · L97 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Exemple de Media Query responsiva per a la graella de mosaics del tauler principal.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** Cap `label=` a les opcions. No és referenciable des del text. |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:media_query_responsive},` a les opcions.

---

#### LST-5.3 · **SENSE LABEL** · L134 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Petició d'enviament de credencials al servidor (PlainLogin).}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:plainlogin_request},`.

---

#### LST-5.4 · **SENSE LABEL** · L160 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Exemples de resposta segons l'escenari d'autenticació.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:auth_responses},`.

---

#### LST-5.5 · **SENSE LABEL** · L177 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Petició d'obtenció de perfil i permisos del context d'usuari.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:whoami_request},`.

---

#### LST-5.6 · **SENSE LABEL** · L214 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Trama de Keep-Alive enviada periòdicament al servidor.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:keepalive_msg},`.

---

#### LST-5.7 · **SENSE LABEL** · L225 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Missatge de subscripció enviat pel client en inicialitzar el canal \texttt{la-core-notifier}.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:subscription_msg},`.

---

#### LST-5.8 · **SENSE LABEL** · L239 — ⚠️ AVÍS

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `caption={Estructura del missatge \texttt{ERRORS\_NOTIFY} rebut pel \textit{frontend}.}` |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** |
| 4 | Underscores escapats | ✅ | Underscore correctament escapat amb `\_` dins `\texttt{}` |
| 5 | Posicionament | ✅ | Inline |

**Acció correctora:** Afegir `label={lst:errors_notify_msg},`.

---

### Figures de la UI — `5_implementacio.tex` (L121–L701)

Les 14 figures de captures de pantalla de la interfície d'usuari segueixen un patró consistent i uniforme. S'auditen conjuntament.

| ID | Label | Línia | Cap. | Font | Label OK | `_` | Pos. |
|----|-------|-------|------|------|----------|-----|------|
| FIG-5.1 | `fig:login` | L121 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.2 | `fig:login2` | L152 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.3 | `fig:seq_login` | L203 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.4 | `fig:ui_home` | L253 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.5 | `fig:ui_home_panels` | L277 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.6 | `fig:ui_license` | L367 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.7 | `fig:ui_errors` | L379 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.8 | `fig:config_system_users` | L404 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.9 | `fig:config_network` | L426 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.10 | `fig:config_create_user` | L453 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.11 | `fig:seq_config_lock` | L573 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.12 | `fig:config_cameras_save` | L624 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.13 | `fig:config_unsaved` | L633 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.14 | `fig:ui_wizards` | L646 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.15 | `fig:ui_proxy` | L667 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.16 | `fig:ui_cameras` | L679 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.17 | `fig:activity_video_connection` | L701 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.18 | `fig:ui_recordings` | L799 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.19 | `fig:alarms_data_flow` | L893 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.20 | `fig:ui_alarms` | L986 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.21 | `fig:ui_update` | L1013 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.22 | `fig:ui_upgrading_flash` | L1035 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.23 | `fig:ui_upgrading_reboot` | L1045 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.24 | `fig:ui_configuration_actions` | L1059 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.25 | `fig:ui_log` | L1076 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |
| FIG-5.26 | `fig:ui_documentation` | L1095 | ✅ | ✅ | ✅ | ✅ | ✅ `[htbp]` |

**Acció correctora:** Cap. Totes les figures del capítol 5 compleixen els 5 punts d'or.

---

### Llistats de Codi amb Issues — `5_implementacio.tex` (continuació)

#### LST-5.9 i LST-5.10 · **SENSE CAPTION (title)** · L312, L336 — 🔴 ERROR CRÍTIC

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | 🔴 | **`title={...}` en lloc de `caption={...}`**. L'opció `title` únicament genera una capçalera visual; l'element **no apareix a la Llista de Llistats** del document i no pot ser referenciat amb `\ref{}`. |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ⚠️ | **ABSENT.** Sense `caption`, no té sentit un label. |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | Inline (dins minipage) |

**Acció correctora:** Substituir `title={\small\texttt{en.json}}` per `caption={\small\texttt{en.json} — Fitxer de traduccions anglès.}, label={lst:i18n_en}` (i equivalent per al fitxer `es.json`). Nota: amb minipage, pot ser necessari un entorn `figure` embolcall per flotar correctament.

---

#### LST-5.11 · `lst:schema_json` · L463

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | Caption descriptiu i adequat |
| 2 | Font d'autoria | ⚠️ | **ABSENT.** |
| 3 | Label correcte | ✅ | `label={lst:schema_json}` a les opcions |
| 4 | Underscores escapats | ✅ | Els `_rule`, `_type` etc. estan dins el codi (no al caption) |
| 5 | Posicionament | ✅ | Inline |

---

#### LST-5.12 a LST-5.26 · **SENSE LABEL** · L523–L996

Els llistats restants del capítol 5 tots tenen `caption={...}` vàlid però manquen de `label=`. Es llisten a continuació amb la proposta de label recomanada.

| ID | Línia | Caption (resum) | Label proposat |
|----|-------|-----------------|----------------|
| LST-5.12 | L523 | Vinculació restriccions numèriques... | `lst:schema_input_binding` |
| LST-5.13 | L533 | Signal `passwordRequirements`... | `lst:password_requirements_signal` |
| LST-5.14 | L562 | Estructura JSON del bloqueig de configuració | `lst:config_lock_json` |
| LST-5.15 | L591 | Mètode `saveConfigNode`... | `lst:save_config_node` |
| LST-5.16 | L601 | Fragment `authInterceptor`... | `lst:auth_interceptor` |
| LST-5.17 | L615 | Actualització referència original... | `lst:update_ref_after_save` |
| LST-5.18 | L741 | Interfície comuna `StreamingStrategy`... | `lst:streaming_strategy_iface` |
| LST-5.19 | L755 | Model unificat `IoItem`... | `lst:io_item_model` |
| LST-5.20 | L769 | Payload `IO_NOTIFY`... | `lst:io_notify_payload` |
| LST-5.21 | L782 | Petició d'activació sortida... | `lst:output_activation_req` |
| **LST-5.22** | **L855** | Càrrega dinàmica HLS... | ✅ **`lst:hls_load_code`** (ja present) |
| **LST-5.23** | **L916** | Funció escolta D-Bus... | ✅ **`lst:dbus_listen_code`** (ja present) |
| LST-5.24 | L942 | Lògica cua thread-safe... | `lst:thread_safe_queue` |
| LST-5.25 | L969 | Trigger BD poda registre d'alarmes | `lst:alarm_prune_trigger` |
| LST-5.26 | L996 | Missatge `ALARM_READ`... | `lst:alarm_read_msg` |

**Acció correctora:** Afegir `label={lst:NOM_PROPOSAT},` a les opcions de cada llistat.

---

## Capítol 6 — Validació (`6_validacio.tex`)

### TAB-6.1 · `tab:latencies` · L37

| # | Criteri | Estat | Detall |
|---|---------|-------|--------|
| 1 | Caption | ✅ | `Comparativa de latència segons protocol de comunicació.` |
| 2 | Font d'autoria | ✅ | `\small Font: Elaboració pròpia mitjançant proves en entorn de laboratori.` (L49) |
| 3 | Label correcte | ✅ | `\label{tab:latencies}` sota caption |
| 4 | Underscores escapats | ✅ | — |
| 5 | Posicionament | ✅ | `[htbp]` |

**Acció correctora:** Cap.

---

## Capítols 7, 8 i Annexos

**Cap element visual** (taula, figura o llistat) detectat en cap d'aquests fitxers.

- `7_integracio_coneixements.tex` — 0 elements
- `8_conclusions.tex` — 0 elements
- `annexos.tex` — 0 elements (fitxer pràcticament buit)

---

## Pla d'Acció Consolidat

### 🔴 Errors Crítics (2 elements) — Prioritat Màxima

| Ref | Fitxer | Línia | Problema | Acció |
|-----|--------|-------|----------|-------|
| LST-5.9 | `5_implementacio.tex` | L312 | `title=` en lloc de `caption=` — no indexat a la Llista de Llistats | Substituir `title=` per `caption=` i afegir `label=` |
| LST-5.10 | `5_implementacio.tex` | L336 | Ídem | Ídem |

---

### ⚠️ Avisos — Atribució de Font absent (44 elements)

| Grup | Fitxer | Nombre | Acció estandarditzada |
|------|--------|--------|----------------------|
| Taules UC (UC-00 a UC-06) | `3_requisits.tex` | 7 | Afegir `\par\smallskip\small Font: Especificació pròpia...` |
| Taules RNF (RNF-01 a RNF-08) | `3_requisits.tex` | 8 | Afegir `\par\smallskip\small Font: Elaboració pròpia (VOLERE)...` |
| `tab:llicencies` | `3_requisits.tex` | 1 | Afegir `\par\smallskip\small Font: Elaboració pròpia...` |
| `tab:matriu_comunicacio` | `4_arquitectura.tex` | 1 | Afegir `\par\smallskip\small Font: Elaboració pròpia.` |
| `lst:nginx_routing`, `lst:nginx_security` | `4_arquitectura.tex` | 2 | Afegir nota de font post-llistat |
| Llistats de codi (tots) | `5_implementacio.tex` | 25 | Afegir Font al caption o com a nota post-llistat |

---

### ⚠️ Avisos — Labels absents (21 elements)

Tots es troben a `5_implementacio.tex`. Consultar la taula de labels proposats a la secció LST-5.12 a LST-5.26. Els noms proposats segueixen la convenció `lst:nom_descriptiu_snake_case`.

---

### ⚠️ Avís — Posicionament rígid (1 element)

| Ref | Fitxer | Línia | Posició actual | Posició recomanada |
|-----|--------|-------|----------------|-------------------|
| TAB-3.16 | `3_requisits.tex` | L449 | `[h!]` | `[htbp]` |

---

### ✅ Elements sense cap problema (42 elements)

- Capítol 1: 1 taula ✅
- Capítol 2: 6 taules + 1 figura ✅
- Capítol 3: 2 figures ✅
- Capítol 4: 4 figures ✅
- Capítol 5: 1 taula + 26 figures ✅ (totes les figures)
- Capítol 6: 1 taula ✅

---

*Informe generat el 2026-06-22. Per a qualsevol dubte metodològic, contactar amb l'autor del TFG (jan.rosell@lanaccess.com).*
