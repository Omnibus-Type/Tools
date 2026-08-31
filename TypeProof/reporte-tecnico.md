# Type Proof — Reporte técnico

Documento de desarrollo. Registra el objetivo, las decisiones técnicas, las mediciones que fundamentan las decisiones tipográficas, la arquitectura del código y las limitaciones conocidas.

Objeto documentado: `type-proof.html`, archivo único de 110 KB. La interfaz está en inglés; este documento, en castellano.

---

## 1. Objetivo y punto de partida

El desarrollo parte de la *Font Testing Page* de Pablo Impallari y Pablo Cosgaya, publicada hacia 2012. La versión original es una aplicación multiarchivo que requiere servidor: PHP para incluir fragmentos y generar bucles de cuerpos, jQuery 1.7.2 para la interacción, y una decena de archivos de estilos, scripts y contenido.

El objetivo fue reconstruirla como un archivo único que se abra con doble clic, sin servidor, sin dependencias y sin paso de compilación, conservando el espíritu de la herramienta y modernizando la carga de fuentes, el control tipográfico y la interfaz.

Se auditó el `index.php` original para relevar la arquitectura y el repertorio de vistas. Los archivos satélite no eran recuperables desde el entorno de trabajo, pero el archivo principal expone la estructura completa.

## 2. Decisiones técnicas de fondo

**Carga de fuentes con `FontFace`.** Se reemplazó el mecanismo original —arrastre con `FileReader` y almacenamiento en `localStorage`— por la API `FontFace` alimentada con el `ArrayBuffer` del archivo. Evita objetos URL y delega el ciclo de vida al navegador.

**Lectura de ejes desde el binario.** Los ejes de las fuentes variables se obtienen parseando la tabla OpenType `fvar` directamente del archivo con un `DataView`. Se eligió por sobre las APIs del navegador porque es el método más portable; existe un respaldo vía `FontFace.variationAxes` para los formatos comprimidos.

**Construcción única del DOM.** Las diez vistas se construyen una sola vez y se alternan por visibilidad. Esto preserva el texto que el usuario edita al cambiar de pestaña, sin necesidad de un almacén de estado aparte.

**Estado en memoria.** No se usa `localStorage` ni `sessionStorage`, de modo que el comportamiento es idéntico abriendo el archivo en local o incrustándolo. El costo es que no hay persistencia entre recargas.

**Tipografías incrustadas.** Chivo y Chivo Mono se incrustaron en base64 dentro del archivo, eliminando la última dependencia externa (§6).

**Interfaz en inglés.** La herramienta se tradujo íntegramente: rótulos, textos de muestra, mensajes de error, nombres legibles de los ejes, comentarios del código y tokens de diseño (`--radius`, `--optical-nudge`, `--key-height`, `--divider`). No quedan cadenas en castellano en el archivo.

## 3. Mediciones tipográficas

Las decisiones de composición no se estimaron a ojo: se midieron sobre los binarios con `fontTools`, instanciando las variables al peso de uso.

### 3.1 Prosos de Chivo (peso 400, 1000 upem)

| Glifo | Proso izquierdo | En em |
|---|---|---|
| C | 63 u | 0,063 |
| H | 91 u | 0,091 |
| A | 10 u | 0,010 |
| b | 79 u | 0,079 |
| d | 57 u | 0,057 |
| l | 79 u | 0,079 |
| R | 91 u | 0,091 |

Estas cifras fundamentan la compensación óptica del rótulo del cargador (§4.2).

### 3.2 Asta de Chivo Mono (peso 400)

El asta vertical mide 99 u, es decir 0,099 em, o 0,99 px al cuerpo de 10 px en que se componen los indicadores de cuerpo. Esta cifra fundamenta el trazo de los íconos (§4.3).

### 3.3 Cobertura de glifos

Se verificó que los subconjuntos latinos incrustados cubren la totalidad de los acentos y signos del español —á é í ó ú ñ ü, ¿ ¡— más los signos tipográficos en uso: em dash, punto medio y comillas latinas. Sin faltantes.

## 4. Sistema de composición de la interfaz

### 4.1 Composición del encabezado

El bloque de marca —título de 19/18 px más bajada, separados por 5 px, con 36 px de altura total— se centra en la altura del rectángulo de carga, de 43 px, con 3,5 px arriba y abajo.

La bajada adopta el tratamiento de los títulos de sección del panel: 9 px, versales, tracking .20 em y el gris de baja jerarquía. Se conserva su interlínea de 13 px precisamente para que el bloque siga midiendo 36 px y el centrado no se altere.

Una etapa previa del desarrollo alineó ese bloque por línea de base: la del título coincidía con la de «LOAD FONT» y la de la bajada con el borde inferior del rectángulo. Como las interlíneas en `normal` dependen de métricas internas no calculables, se fijaron interlíneas explícitas y se resolvió el sistema de ecuaciones correspondiente. El criterio se sustituyó luego por el centrado en altura, que es el vigente; las interlíneas explícitas se conservan porque hacen predecible la caja.

### 4.2 Compensación óptica del cargador

El rótulo «LOAD FONT» se percibía desplazado a la izquierda respecto de los ejemplos. El diagnóstico tiene dos componentes: la C redonda tiene proso de 0,063 em contra 0,079 em de un asta, y —dominante— los prosos escalan con el cuerpo, de modo que a 9 px el rótulo arranca casi sobre el margen mientras un ejemplo a 24 o 60 px arranca varios píxeles más adentro.

Se aplicó un desplazamiento de 2 px al rótulo, equivalente al eje de astas de un texto de unos 24 px, expuesto en la variable `--optical-nudge`. La fórmula para anclarlo a otro cuerpo es 0,079 × cuerpo − 0,57 px.

Queda registrada la limitación: como los ejemplos van de 8 a 120 px, no existe un eje de astas único; se tomó el cuerpo de lectura como referencia.

### 4.3 Trazo de los íconos

Los íconos se dibujan en un lienzo de 32 unidades y se muestran a 15 px, con un factor de escala de 0,469. Para que su espesor equivalga al asta de los indicadores de cuerpo (0,99 px), el trazo se fijó en 2,1, que rinde 0,984 px. El valor original de 1,4 rendía 0,656 px, de ahí que el ícono se viera más liviano que el texto contiguo.

Los dos íconos de la herramienta —el de carga y el de ejes variables— comparten construcción: solo astas rectas, sin figuras cerradas, mismo trazo de 2,1, remates y uniones redondeados, y la misma extensión dentro del lienzo.

### 4.4 Alineación de la grilla

El encabezado replica la grilla del lienzo: columna de 218 px equivalente al panel lateral, y adentro columnas de 3,6 rem con 20 px de separación, idénticas a las de las filas de specimen. El ícono cae sobre el eje de los indicadores de cuerpo (262 px) y el rótulo sobre el margen de los ejemplos (339,6 px).

El cargador y las fichas de fuentes comparten una grilla de columnas iguales (`grid-auto-columns: minmax(0,1fr)`), de modo que cada fuente cargada suma una celda del mismo ancho. Las fichas se ubican antes que el cargador mediante `order`, sin alterar el orden del marcado, y el cargador cede protagonismo —contorno y rótulo atenuados— una vez que hay una fuente en uso. En consecuencia, la coincidencia del ícono con el eje de los indicadores rige en el estado inicial; con fuentes cargadas el cargador se desplaza y adopta un relleno simétrico.

Dentro del panel, todo converge en dos ejes verticales: 18 px a la izquierda —margen de los títulos de sección, del signo antepuesto a las vistas, de la primera columna de funciones OpenType y de los deslizadores— y 199 px a la derecha, cierre de la cuarta columna de funciones y de los valores numéricos.

### 4.5 Organización del tablero

El panel se ordena en seis grupos: *Views*, *OpenType features*, *Composition*, *Variable axes*, *Text* y *Canvas*. El criterio es que cada grupo reúna comandos que operan sobre el mismo objeto. *Text* agrupa los dos que afectan el contenido —activar la edición y restablecer los textos originales—; *Canvas* los que afectan el soporte —inversión, papel y tinta—. Una etapa previa tenía el restablecimiento aislado al pie del panel, lejos de los comandos con los que se relaciona.

El aviso de la sección *Variable axes* se compone como una caja emparentada con el cargador —mismo rectángulo, misma estructura de ícono más rótulo, mismo radio— pero con borde continuo en lugar de punteado, para que no se lea como una segunda zona de arrastre.

### 4.6 Sistema de formas, medidas y escala

| Recurso | Valor | Alcance |
|---|---|---|
| Radio | `--radius: 5px` | Todos los rectángulos del tablero |
| Altura de tecla | `--key-height: 25px` | *editable*, *reset text*, *invert*, *Paper*, *Ink* |
| Divisoria estructural | `--divider: 0.20` | Reglas entre módulos: encabezado, panel, pie |
| Borde de control | `--line: 0.10` | Contornos de teclas, fichas y funciones |
| Gris bajo | `--t-30` | Todo el nivel de menor jerarquía |
| Cuerpo bajo | 9 px | Rótulos, avisos, metadatos, controles |
| Cuerpo medio | 10 px | Navegación, indicadores de cuerpo, pie |
| Tracking de rótulos | .20 em | Rótulos en versales del nivel bajo |
| Tracking de oraciones | .02 em | Bajada, pie y aviso de ejes, en caja baja |

Quedan fuera del token de radio, a propósito, el riel del deslizador, los círculos de color y el thumb de la barra de scroll, que no son rectángulos.

La altura de tecla se resolvió con `min-height` y centrado por flex, no compensando rellenos: las cajas naturales diferían —19 px las de solo texto, 20 px las que contienen el círculo de color de 14 px— y el resultado dependía de las métricas de la fuente. Con la altura mínima gobernando ambas, las cinco teclas miden exactamente lo mismo.

Las divisorias entre módulos tienen token propio, al doble de contraste que los bordes de control. Separarlas evita que aclarar la estructura engorde también el contorno de cada tecla.

En los controles de composición, el valor numérico hereda el color de su rótulo: la distinción entre ambos queda a cargo de la tipografía —Chivo para el nombre, Chivo Mono para la cifra— y no del contraste.

### 4.7 Controles de composición

El espaciado se expresa en milésimas de em y se lee en em con tres decimales. El rango original —−60 a +200— era asimétrico: el lado negativo alcanzaba apenas un tercio del recorrido positivo y se percibía como inoperante. Se amplió a −100, es decir −0,100 em, límite por debajo del cual la mayoría de las tipografías pierde legibilidad.

La interlínea pasó de actuar solo sobre las vistas de texto a hacerlo sobre todas. Para no aplanar el ritmo propio de cada vista, se implementó como factor: cada vista declara su base —1,05 en las de despliegue, 1,40 en las de texto— y el control la multiplica entre 0,60 y 2,00 mediante `calc()`. En el valor neutro el aspecto es idéntico al diseñado. Al cambiar de vista, espaciado e interlínea vuelven a sus valores neutros para presentar cada vista con su composición original.

### 4.8 Adaptación a viewport de teléfono

Por debajo de 760 px el panel deja de ser columna lateral y se convierte en una banda sobre el lienzo. Como el recurso escaso pasa a ser el alto, la compresión es vertical: los deslizadores se disponen en dos columnas, las funciones OpenType pasan de cuatro a seis columnas —dos filas en lugar de tres— y se reducen los rellenos. Un segundo punto de quiebre en 460 px devuelve las funciones a cuatro columnas y apila los deslizadores.

Las vistas pasan a una tira horizontal desplazable. El signo antepuesto se suprime ahí: funciona como sangría en una lista vertical, pero en una fila resulta ruido y no separa. En su lugar, cada pestaña adopta el rectángulo con borde del resto del panel, que es lo que las distingue en disposición horizontal. El contador de filas se oculta por la misma razón de economía.

## 5. Contenido de muestra y escalas

Las escalas de cuerpo no se eligen por vista de manera independiente: salvo las de texto, se toman de la ladera establecida en *Headlines* (120, 96, 72, 60, 48, 36, 30, 24, 18, 14, 12), para no multiplicar laderas paralelas.

| Vista | Escala |
|---|---|
| Headlines | 120 96 72 60 48 36 30 24 18 14 12 |
| Text | 24 18 16 14 12 11 10 |
| adhesion / hamburg | 120 84 60 42 30 |
| a–z A–Z 0–9 | 72 60 36 18 |
| Words / Caps | 60 |
| Diacritics | 96 72 48 36 24 18 12 |
| Kerning | 96 72 48 36 24 18 |
| Small sizes | 16 14 12 11 10 9 8 6 |

Ambas laderas arrancan en 96, cuerpo en el que tanto los pares como el acomodo de los diacríticos se juzgan con holgura.

En *Text* la medida es de 37 em y en *Small sizes* de 34 em: la primera se ensanchó para que el párrafo de muestra se resuelva en una línea menos.

*Words* y *Caps* usan el ciclo fonético OTAN completo, de Alfa a Zulu, en caja mixta y en versales. La elección cubre las veintiséis iniciales del alfabeto con palabras reales de longitud dispar, y respeta la grafía oficial —Alfa sin ph, Juliett con doble te, X-ray con guion—, que además aporta un caso de guion a la prueba.

*adhesion* se compone en una sola línea, «minimum adhesion nonillion», que concentra las secuencias clásicas de ritmo vertical.

Los indicadores del margen se expresan en píxeles CSS, que es la unidad que aplica el navegador.

## 6. Incrustación de las tipografías

Se incrustaron los subconjuntos latinos de las versiones variables de Chivo y Chivo Mono, en `.woff2`, codificados en base64 dentro de dos reglas `@font-face`.

| Fuente | Peso del binario | Eje |
|---|---|---|
| Chivo | 32,4 KB | `wght` 100–900 |
| Chivo Mono | 25,7 KB | `wght` 100–900 |

Los archivos se obtuvieron vía Fontsource, que redistribuye los binarios de Google Fonts. Se verificó la firma `wOF2` de ambos tras la codificación.

El archivo pasó de unos 27 KB a 110 KB. A cambio, no queda ninguna llamada a servidores de terceros: la interfaz se compone en Chivo aunque no haya conexión, en coherencia con que las fuentes bajo prueba tampoco salen del navegador.

## 7. Arquitectura del código

### 7.1 Estado

```js
state = {
  fonts: [ { name, family, format, size, axes, vals } ],
  active,    // índice de la fuente activa
  view,      // id de la vista visible
  counter    // generador de familias únicas
}
```

Configuración en tres estructuras: `DEFAULTS` con los textos de muestra, `VIEWS` con las diez vistas y su función constructora, y `FEATURES` con las doce funciones y su estado inicial.

Cada entrada de `VIEWS` admite una marca opcional `keepCase`, que anula la conversión a versales de la pestaña. La usa únicamente *a–z A–Z 0–9*, cuya etiqueta debe conservar su caja tipográfica literal.

### 7.2 Parseo de la tabla `fvar`

El procedimiento inspecciona la firma del archivo: devuelve `null` ante `wOFF` o `wOF2` (comprimidos, no legibles en crudo), y toma el desplazamiento de la primera fuente ante una colección `ttcf`. Luego recorre el directorio de tablas sfnt buscando el registro `fvar`.

Dentro de `fvar`, la cabecera indica en +4 el inicio del arreglo de ejes, en +8 la cantidad y en +10 el tamaño de cada registro. Cada `VariationAxisRecord`:

| Desplazamiento | Tipo | Campo |
|---|---|---|
| +0 | Tag (4 bytes) | Etiqueta del eje |
| +4 | Fixed 16.16 | Valor mínimo |
| +8 | Fixed 16.16 | Valor por defecto |
| +12 | Fixed 16.16 | Valor máximo |
| +16 | uint16 | Flags |
| +18 | uint16 | ID de nombre |

Los valores `Fixed` se leen como enteros de 32 bits con signo divididos por 65536.

El parser se validó contra una fuente variable real, leyendo correctamente su eje y su rango.

### 7.3 Referencia de funciones

| Función | Responsabilidad |
|---|---|
| `buildAll()` | Construye las vistas y sus pestañas |
| `switchView(id)` | Alterna la vista visible |
| `resetComposition()` | Devuelve espaciado e interlínea a sus valores neutros |
| `row` / `line` / `para` / `blockLine` | Ayudantes de construcción de specimens |
| `applyFeatures()` | Aplica `font-feature-settings` |
| `loadFiles(fileList)` | Carga, valida y registra fuentes |
| `parseAxes(buf)` | Parsea la tabla `fvar` |
| `axesFromFontFace(ff)` | Respaldo vía `variationAxes` |
| `buildAxes()` | Genera los deslizadores de ejes |
| `applyVariation()` | Aplica `font-variation-settings` |
| `setActive(i)` | Activa una fuente y refresca la interfaz |
| `renderChips()` | Pinta las fichas de fuentes |
| `applyColors()` / `luminance(hex)` | Tematización del lienzo |
| `humanSize` / `escapeHtml` | Utilidades de formato |
| `showToast(msg)` | Aviso emergente de error |

## 8. Compatibilidad

| API | Nota |
|---|---|
| `FontFace` + `ArrayBuffer` | Carga en cliente; soporte amplio |
| `DataView` | Lectura binaria de `fvar` |
| `font-feature-settings` | Funciones OpenType; soporte amplio |
| `font-variation-settings` | Ejes variables; soporte amplio |
| `font-optical-sizing: none` | Prioriza el eje `opsz` manual sobre el ajuste automático |
| `FontFace.variationAxes` | Respaldo para WOFF/WOFF2; **no universal** |
| `backdrop-filter` | Capa de arrastre |
| Custom properties | Tokens de diseño |

## 9. Limitaciones y trabajo futuro

Sin persistencia de estado entre recargas; `localStorage` es el primer candidato si se desea incorporarla.

Los ejes de fuentes en `.woff` y `.woff2` dependen del respaldo del navegador. La solución robusta sería incorporar un descompresor —inflate para WOFF, Brotli para WOFF2— antes de parsear.

Los ejes se exponen como rangos continuos; falta el selector de instancias con nombre, que también residen en `fvar`. Falta asimismo una grilla completa de glifos leyendo la tabla `cmap`, la exportación a PDF con saltos limpios y la comparación de dos fuentes lado a lado.

Los subconjuntos incrustados son latinos: textos de muestra en griego o cirílico exigirían incrustar también esos subconjuntos.

En *Small sizes*, la escala desciende de uno en uno entre 12 y 8, y el último escalón salta a 6: queda pendiente decidir si se intercala el 7.

## 10. Privacidad

Las fuentes se procesan íntegramente en el navegador mediante `FontFace`; nada se sube a ningún servidor. Tras la incrustación de las tipografías de interfaz no queda tráfico de red externo alguno. El texto de los specimens se edita localmente y no se transmite.

## 11. Créditos

Type Proof es un desarrollo de Pablo Impallari y Pablo Cosgaya, basado en Fontdrag y FontTest de Dave Crossland. Versión HTML + JavaScript compuesto con Chivo y Chivo Mono.
