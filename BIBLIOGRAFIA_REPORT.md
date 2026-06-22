# Informe de Bibliografía — BIBLIOGRAFIA_REPORT.md

> **Data de generació:** 22 de juny de 2026  
> **Fitxers analitzats:** `references.bib` + tots els `.tex` de `capitols/` i `main.tex`  
> **Mètode:** Creuament exhaustiu de claus `@entry{key,` del `.bib` vs. tots els `\cite{key}` del projecte.

---

## Resum Executiu

| Indicador | Resultat |
|-----------|----------|
| Entrades totals al `.bib` | 43 |
| Cites totals als `.tex` | 35 claus úniques |
| **Cites trencades** (`\cite{}` sense entrada al `.bib`) | **0** |
| **Referències òrfenes** (al `.bib` però mai citades) | **9** |

---

## Secció 1 — Cites Trencades

**Resultat: CAP**

Totes les claus usades amb `\cite{}` al llarg de la memòria existeixen al fitxer `references.bib`. No hi ha cap cita trencada que provoqui errors de LaTeX.

---

## Secció 2 — Referències Òrfenes

Les 9 entrades següents estan definides al `.bib` però **no es criden mai** amb `\cite{}` en cap fitxer `.tex`. Apareixeran a la bibliografia compilada **si i només si** s'usen `\nocite{}` o `\bibliography` en mode `all`; en cas contrari, no apareixeran al document final.

---

### 1. `cleancode` — @book

| Camp | Valor |
|------|-------|
| Autor | Robert C. Martin |
| Títol | *Clean Code: A Handbook of Agile Software Craftsmanship* |
| Editorial | Prentice Hall |
| Any | 2008 |
| ISBN | 978-0132350884 |

**Context potencial:** Podria citar-se en capítols de metodologia, bones pràctiques de desenvolupament, o en la discussió sobre l'aplicació dels principis SOLID (que sí que es cita via `solid_principles`).

---

### 2. `cypress_testing` — @online

| Camp | Valor |
|------|-------|
| Autor | Cypress.io |
| Títol | *Cypress Modern End-to-End Testing Framework* |
| Any | 2025 |
| URL | https://www.cypress.io |

**Context potencial:** Cypress és un framework de testing E2E; podria citar-se al capítol de proves si es va avaluar com a alternativa a Playwright (que sí que es cita via `playwright_testing`).

---

### 3. `git_version_control` — @book

| Camp | Valor |
|------|-------|
| Autors | Jon Loeliger, Matthew McCullough |
| Títol | *Version Control with Git* (2a ed.) |
| Editorial | O'Reilly Media |
| Any | 2012 |
| ISBN | 978-1449316389 |

**Context potencial:** Podria citar-se al capítol de planificació o metodologia quan es descriu l'ús de Git com a eina de control de versions del projecte.

---

### 4. `grpc_up_running` — @book

| Camp | Valor |
|------|-------|
| Autors | Kasun Indrasiri, Danesh Kuruppu |
| Títol | *gRPC: Up and Running* |
| Editorial | O'Reilly Media |
| Any | 2020 |
| ISBN | 978-1492058335 |

**Context potencial:** Entrada duplicada conceptualment amb `grpc_io` (que sí que es cita). Ambdues cobreixen gRPC; la versió del llibre `grpc_up_running` podria complementar la referència online per donar suport bibliogràfic més acadèmic.

---

### 5. `rfc9113` — @techreport (RFC)

| Camp | Valor |
|------|-------|
| Autor | M. Bishop (IETF) |
| Títol | *HTTP/2* |
| Número | RFC 9113 |
| Any | 2022 |
| URL | https://datatracker.ietf.org/doc/html/rfc9113 |

**Context potencial:** HTTP/2 és el protocol de transport de gRPC. Podria citar-se a l'arquitectura o implementació quan es descriu la comunicació entre microserveis via gRPC, en paral·lel a `rfc8825` (WebRTC Overview) que sí que es cita.

---

### 6. `sqlite_spec` — @online

| Camp | Valor |
|------|-------|
| Autor | SQLite Consortium |
| Títol | *SQLite Architecture and Database Engine* |
| Any | 2024 |
| URL | https://www.sqlite.org/arch.html |

**Context potencial:** Si SQLite s'esmenta a la implementació com a base de dades (p. ex. per a configuració local o en algun microservei), podria afegir-se `\cite{sqlite_spec}` en aquell punt.

---

### 7. `typescript_spec` — @online

| Camp | Valor |
|------|-------|
| Autor | Microsoft Corporation |
| Títol | *TypeScript Language Specification* |
| Any | 2024 |
| URL | https://www.typescriptlang.org/docs/ |

**Context potencial:** TypeScript és el llenguatge del front-end Angular. Podria citar-se al capítol de Stack Tecnològic o Implementació quan es presenta Angular/TypeScript com a elecció de tecnologia.

---

### 8. `webrtc_org` — @online

| Camp | Valor |
|------|-------|
| Autor | WebRTC Open Source Project |
| Títol | *WebRTC Architecture* |
| Any | 2024 |
| URL | https://webrtc.org/architecture/ |

**Context potencial:** Entrada duplicada conceptualment amb `webrtc_w3c` (també òrfena). Cobreix l'arquitectura WebRTC. Malgrat que WebRTC es cita àmpliament via els RFCs (`rfc8445`, `rfc8825`, `rfc9716`, `rfc3711`), no s'ha citat cap d'aquestes dues entrades de la web oficial.

---

### 9. `webrtc_w3c` — @online

| Camp | Valor |
|------|-------|
| Autor | W3C WebRTC Working Group |
| Títol | *WebRTC 1.0: Real-Time Communication Between Browsers* |
| Any | 2024 |
| URL | https://www.w3.org/TR/webrtc/ |

**Context potencial:** Especificació W3C de WebRTC 1.0. Complementa `webrtc_org`; totes dues òrfenes. Podria citar-se al capítol d'Arquitectura quan es presenta el protocol WebRTC com a base del sistema de streaming en temps real.

---

## Secció 3 — Observacions i Recomanacions

### 3.1 Duplicitats conceptuals al `.bib`

Dues parelles d'entrades cobreixen el mateix tema però des de fonts diferents; una de cada parella és citada i l'altra és òrfena:

| Tema | Entrada citada | Entrada òrfena |
|------|----------------|----------------|
| gRPC | `grpc_io` (web oficial) | `grpc_up_running` (llibre O'Reilly) |
| WebRTC | RFCs (8445, 8825, 9716, 3711) | `webrtc_org` + `webrtc_w3c` |

Per a un TFG, **citar el llibre `grpc_up_running` juntament amb `grpc_io`** aportaria una font bibliogràfica més acadèmica. Igualment, afegir `webrtc_w3c` (especificació W3C) donaria solidesa a les seccions WebRTC.

### 3.2 Tecnologies del projecte sense cita

Les entrades `typescript_spec` i `sqlite_spec` estan al `.bib` però cap capítol les cita. Si TypeScript i/o SQLite es mencionen al text sense referència, seria recomanable afegir `\cite{typescript_spec}` i `\cite{sqlite_spec}` en els punts corresponents.

### 3.3 Control de versions i qualitat de codi

`git_version_control` i `cleancode` no estan citats. Si el capítol de Metodologia menciona Git com a eina o Clean Code / SOLID com a principis de disseny, afegir aquestes cites reforçaria la base bibliogràfica.

### 3.4 Testing E2E

`cypress_testing` és òrfena. Si el capítol de proves conclou que es va triar Playwright en lloc de Cypress, podria citar-se `cypress_testing` en la justificació de l'elecció (`playwright_testing` sí que es cita).

---

*Fi de l'informe — cap fitxer `.tex` ni `.bib` ha estat modificat.*
