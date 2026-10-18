---
name: "revision-iso27001-pacer"
description: "Revisar un documento del SGSI de Pacer (ISO 27001, set de Hackmetrix) contra la base pacer-mcp-iso27001 y redactar el veredicto y el mensaje de revisión para Lisandro."
---

# Revisión de documentos ISO 27001 de Pacer

Alan (CTO) aprueba los documentos del SGSI que redacta Lisandro (DevSecOps Lead / OSI) en Hackmetrix. Esta skill aplica siempre el mismo método de revisión y termina en un veredicto y un mensaje listo para dejar en la revisión del documento.

## Herramientas

- **Base de conocimiento:** `pacer-mcp-iso27001` → herramienta `qdrant-find` (en esta sesión aparece como `mcp__remote-devices__pacer-mcp-iso27001__qdrant-find`). Requiere la VPN activa y la app de escritorio abierta. Si no responde, avisar a Alan que reconecte la VPN y no inventar datos.
- **Nunca usar `qdrant-store`** en la base de ISO: solo Alan y Lisandro suben archivos.
- **PDFs:** extraer el texto con `pdftotext` (más barato que leer páginas como imagen). Para ver formato, leer las páginas con Read.

## Qué hay en la base

- `documentos/` → espejo de cada documento (frontmatter con estado, versión, controles, `referencia_a` y bitácora).
- `framework-hackmetrix/` → ficha de cada documento: qué SÍ y qué NO debe incluir, mínimo viable certificable, cláusulas y controles cubiertos.
- `decisiones/` → DEC (decisiones de Pacer), RUL (criterios de Mike/Hackmetrix), GAP (brechas con su tarea de Linear).
- `_reference/` → texto de cada control del Anexo A.
- `organizacion/` y `estado/` → realidad operativa, stack, tracker y consultas pendientes.

La búsqueda es semántica y no lista archivos: hacer varias consultas en paralelo con frases concretas (código del documento, título, la frase dudosa del texto).

## Método

1. **Leer el documento entero** e identificar código (POL/PRO/MET/MAT/PLN/DOC/GUI/ALC), versión y fecha.
2. **Si es una versión corregida** de un documento ya revisado, comparar frase por frase contra la versión anterior (difflib sobre las oraciones) y verificar que cada observación quedó resuelta y que no se rompió nada más.
3. **Traer contexto en paralelo:** el espejo del documento, su ficha de Hackmetrix y las decisiones que lo mencionan (buscar por código y por tema).
4. **Validar cada referencia** a otro documento ("conforme al Procedimiento X"):
   - que el documento exista en el set (o esté previsto y no escrito, que se informa como pendiente, no como error);
   - que **realmente regule** lo que se le atribuye (buscar la sección destino);
   - detectar **referencias circulares** (A manda a B, B manda a A, nadie lo define);
   - que los documentos citados en el cuerpo estén en Referencias.
5. **Cruzar con documentos hermanos** (los auditores leen el par política/procedimiento junto, RUL-0075): misma regla, misma frase, mismo plazo, mismo responsable.
6. **Cruzar con decisiones vigentes:** una DEC posterior al documento puede dejarlo desactualizado (ej.: DEC-0126 Backoffice, DEC-0132 retención de 6 años). Verificar siempre la decisión más reciente sobre el tema.
7. **Verificar contra la realidad:** si el documento afirma un control como hecho, buscar si hay un GAP o una consulta que diga lo contrario. Una afirmación falsa y verificable por el auditor es un error. Un control objetivo con tarea de Linear abierta NO es error (DEC-0001): va a "Para la auditoría".
8. **Cumplimiento ISO:** comparar con el mínimo viable de la ficha y con los controles del Anexo A que declara. Señalar si algún control declarado queda débil.

## Lo que NO se marca como hallazgo

- Desfase entre la fecha del Versionado y la del espejo: se repasa al final (DEC-0076, RUL-0046). Mencionar como menor como mucho.
- Que el documento describa el estado objetivo y no la foto de hoy, si hay tarea de Linear (DEC-0001, R-016).
- Referencias a documentos previstos todavía no escritos: se informan como pendientes.
- Que apruebe el CTO y no el Comité (RUL-0078): es correcto.
- Gold-plating: no pedir controles por encima del mínimo viable (RUL-0048).

## Severidad

- **Bloquea la aprobación:** contradicción con otro documento o decisión vigente; afirmación falsa verificable; referencia que no regula lo atribuido o circular; control declarado sin sustento en ningún documento.
- **Menor:** referencias faltantes en la sección final, redacción, controles opcionales, formato del PDF (páginas vacías y tablas cortadas suelen ser del paginador de la plataforma, RUL-0073).
- **Para la auditoría:** evidencia que hay que tener lista (tareas de Linear, GAPs abiertos).

## Salida

1. **Veredicto por documento:** Aprobar / Aprobar con cambios (indicar cuáles antes de aprobar) / No aprobar. Si son varios, una tabla.
2. **Detalle**, separado en: Para corregir (con texto propuesto entre comillas, listo para pegar), Menores, Referencias e ISO, Para la auditoría.
3. **Mensaje para Lisandro** con la herramienta de redacción de mensajes (tipo "other"), en la voz de Alan:
   - voseo rioplatense, directo, sin saludos largos;
   - abre con qué está bien y cuántas cosas hay que corregir;
   - puntos numerados con la sección, el problema, la decisión o el documento que lo respalda (código DEC/RUL/GAP) y la redacción propuesta como cita;
   - "Menores" y "Para la auditoría" al final;
   - cierra con "Con el punto X resuelto, la apruebo".
4. Avisar a Alan qué supuestos tomó la revisión (umbrales, fechas, quién mide) para que los confirme antes de mandar.

## Regla de oro

La revisión prepara la decisión; **la aprobación en Hackmetrix la hace Alan**. Nunca aprobar, rechazar ni comentar en la plataforma de forma automática.