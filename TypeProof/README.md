# Type Proof

Página de prueba tipográfica autocontenida. Un único archivo HTML que permite cargar una fuente local y examinarla en distintos contextos y cuerpos, con control de funciones OpenType y de los ejes de las fuentes variables. La interfaz está en inglés.

Type Proof es un desarrollo de Pablo Impallari y Pablo Cosgaya, basado en Fontdrag y FontTest de Dave Crossland. Versión HTML + JavaScript compuesto con [Chivo](https://fonts.google.com/specimen/Chivo) y [Chivo Mono](https://fonts.google.com/specimen/Chivo+Mono), incrustadas en el archivo.

---

## Screenshots

![Alphabet](https://github.com/Omnibus-Type/Tools/blob/organize/TypeProof/screenshots/alphabet.png)

## Uso

Descargá `type-proof.html` y abrilo con doble clic en cualquier navegador moderno. No requiere instalación, servidor ni conexión a internet.

Para probar una tipografía, arrastrala sobre la página o usá el botón **Load font**. Acepta `.ttf`, `.otf`, `.woff` y `.woff2`. Podés cargar varias y alternar entre ellas con las fichas del encabezado.

Las fuentes se procesan íntegramente en tu navegador. Nada se sube a ningún servidor.

## Qué incluye

**Diez vistas de specimen**, en el panel *Views*: Headlines (de 120 a 12), Text, adhesion, hamburg, a–z A–Z 0–9, Words, Caps, Diacritics, Kerning y Small sizes (hasta 6). Cada renglón lleva su cuerpo indicado al margen, a modo de hoja de prueba. Words y Caps usan el ciclo fonético OTAN completo, de Alfa a Zulu, que cubre las veintiséis iniciales.

**Textos editables.** Todos los ejemplos se editan en vivo: hacés clic y escribís tu propio texto. Las ediciones se conservan al cambiar de vista. En el grupo *Text*, el botón *editable* activa o desactiva la edición y *reset text* devuelve los originales.

**Funciones OpenType.** Doce toggles: `kern`, `liga` y `calt` activas por defecto, más `dlig`, `smcp`, `onum`, `lnum`, `tnum`, `frac`, `zero`, `salt` y `ss01`.

**Ejes variables.** Si la fuente es variable, se leen sus ejes de la tabla `fvar` y se genera un deslizador por cada uno —`wght`, `wdth`, `opsz`, `ital`, `slnt` o los que declare— con su rango real. Cada fuente cargada recuerda sus propios valores.

**Composición.** *Tracking* de −0,100 a +0,200 em y *Leading* como factor de 0,60 a 2,00. La interlínea actúa sobre todas las vistas: cada una conserva su ritmo propio —ceñido en las de despliegue, abierto en las de texto— y el control lo multiplica. Ambos vuelven a sus valores neutros al cambiar de vista, para que cada vista se presente con su composición original.

**Lienzo.** En el grupo *Canvas*, color de papel y de tinta e inversión para proofear en positivo o negativo.

## Requisitos

Un navegador moderno (Chrome, Firefox, Safari o Edge en versiones recientes). Se apoya en `FontFace`, `font-variation-settings` y `font-feature-settings`.

Funciona en escritorio y en teléfono. En viewport angosto el panel se convierte en una banda sobre el lienzo: las vistas pasan a una tira horizontal desplazable y los controles se comprimen a dos columnas, con un segundo ajuste por debajo de 460 px.

## Limitaciones

El estado no se persiste: al recargar se pierden las fuentes cargadas y los textos editados.

La lectura de ejes en crudo cubre `.ttf`, `.otf` y colecciones `.ttc`. Los formatos `.woff` y `.woff2` van comprimidos, así que dependen de un mecanismo de respaldo que no todos los navegadores exponen; una fuente variable en `.woff2` podría no mostrar ejes en algunos navegadores. Probala como `.ttf` si eso ocurre.

Todavía no hay selector de instancias con nombre ni grilla completa de glifos.

## Estructura

Un solo archivo, `type-proof.html`, de unos 110 KB, con el marcado, los estilos y la lógica embebidos, más las tipografías de interfaz incrustadas en base64. Sin framework, sin paso de compilación, sin backend.

Para extenderlo: las vistas se declaran en el arreglo `VIEWS`, las funciones en `FEATURES` y los nombres legibles de los ejes en `AXIS_NAMES`. La construcción del resto es dinámica.
