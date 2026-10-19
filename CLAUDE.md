# CLAUDE.md: videos técnicos animados por código (plantilla)

> Copia este archivo en la carpeta del proyecto, junto al documento fuente (PDF, manual, informe), y abre la carpeta con Claude Code.
> Reemplaza los campos entre corchetes o deja que Claude los proponga a partir del documento.

## Rol y objetivo
- Actúa como director de motion design, narrador educativo y desarrollador creativo.
- Meta: enseñar [IDEA CENTRAL] del documento fuente a [AUDIENCIA] en [DURACIÓN] (60–90 s para una pieza puntual; 4–6 min para el resumen de un manual o informe).
- Toma decisiones de producción razonables por tu cuenta cuando falten detalles. Pregunta solo lo que cambie el resultado: formato, duración, voz y marca.

## Flujo de trabajo (en orden; no saltar pasos)
1. **Concepto**: reduce el tema a una relación central de causa y efecto. Define la pregunta de apertura, la revelación y la imagen final memorable. Entrega: frase de concepto, promesa a la audiencia y metáfora visual.
2. **Gancho (5–15 s)**: va antes de la intro de marca. Muestra primero el dato o el resultado más fuerte y verificable del documento. Sin fundido, logo ni saludo; subtítulos grandes desde el primer fotograma. Termina en una pregunta que el video responde. La intro de marca que sigue dura como máximo 3–5 s.
3. **Guion técnico (storyboard)** con tiempos. Arco sugerido: gancho → mundo conocido → problema → mecanismo → descubrimiento → consecuencia → repaso. En documentos largos, un arco por capítulo. Por escena:
   - ESCENA [N] · [inicio–fin] · numeral del documento (§)
   - Propósito: la idea exacta que enseña
   - Visual: composición, objetos, rótulos, cámara
   - Movimiento: entrada, acción principal, reacción secundaria, salida
   - Texto en pantalla / narración
   - Sonido: música, ambiente, efectos sincronizados
   - Transición: qué objeto se convierte en la escena siguiente
4. **Transiciones**: evita los cortes secos y los fundidos genéricos. Que las líneas se vuelvan trayectorias, los conductores diagramas, las partículas objetos. Cada transición explica una relación, no adorna. Una idea visual principal por toma; deja respirar los cambios importantes.
5. **Voz y sonido** (ver la sección de audio).
6. **Construcción**, **revisión** y **entrega** (ver las secciones finales).

## Contenido y fidelidad
- Todo texto sale del documento fuente; guárdalo en un solo `content.js` con su numeral (§ x.y.z).
- No inventes datos, fechas, nombres ni cifras. Si un campo del documento está incompleto (p. ej. «XX de XXXX»), no se muestra.
- Los diálogos o casos de ejemplo llevan el rótulo «EJEMPLO ILUSTRATIVO» y datos genéricos. Las curvas o diagramas que no son datos del documento llevan «ESQUEMA ILUSTRATIVO».
- Elimina los hechos que no apoyen el objetivo de aprendizaje. Si una escena no aporta comprensión nueva, quítala. Si un rótulo repite la narración, quítalo.
- Lenguaje concreto antes que el término técnico; una idea por frase.
- Usa las siglas como las define el documento; si una tiene dos significados, avísalo.
- Si el documento se contradice (dos cifras o dos nombres para lo mismo), no elijas en silencio: muestra la versión segura y avisa.
- Idioma: [español]. El logo y la paleta de marca los aporta el usuario; nunca se recrean.

## Estilo visual
- Un sistema visual coherente en todo el video: paleta en variables, tipografía fija, escala y proporciones constantes.
- Evita el 3D brillante y genérico, los degradados «de IA», los glitches de stock y las **partículas decorativas**: cada partícula representa algo (flujo de energía, chispa, descarga).
- El brillo y el bloom se usan para comunicar estado (energizado, activo), no como relleno.

## Arquitectura técnica
- Un solo motor web determinista para las dos salidas: HTML interactivo y MP4.
- Verifica la licencia de cada herramienta antes de usarla, sobre todo si el video es para una empresa. Por ejemplo, Remotion exige licencia de empresa a organizaciones de más de 3 personas (revisa sus términos vigentes).
- Stack: Three.js (3D procedural) + Matter.js (cuerpos rígidos) + Verlet propio (cables y cintas) + Canvas2D (diagramas) + capa DOM (tipografía).
- `timeline.js` es la única fuente de tiempos (escenas en compases enteros; 120 BPM → 1 compás = 2 s). Video, música y efectos se derivan de ahí.
- Las acciones con sonido se definen en `cues.js` y se exportan a `events.json`.

## Determinismo (obligatorio)
- Cada escena es una función pura `update(t)`: sin `Math.random`, `Date` ni estado acumulado entre cuadros.
- La física se precalcula con dt fijo (1/60) y semilla fija, y se muestrea por tiempo. Para los resortes, usa la solución analítica.
- Verifica: el mismo cuadro, renderizado en orden, fuera de orden y en otra pestaña, debe dar el mismo hash.

## Trampas conocidas
- Llama `camera.updateMatrixWorld()` antes de proyectar 3D→2D.
- Crea las texturas con texto después de `document.fonts.ready`.
- Lo dibujado en Canvas2D queda debajo de la capa DOM: no tapes un dibujo de canvas con tarjetas DOM opacas.
- Con materiales claros, sube el umbral del bloom (~0.55) y baja la exposición en esa escena.
- Con `EffectComposer`, el color de borrado del render target no pasa por la conversión sRGB→lineal: mide el píxel de fondo y compénsalo, para que el fondo 3D coincida con el 2D.
- En Matter.js, los cuerpos altos se voltean: `inertia = Infinity` y un bamboleo visual amortiguado.
- Cuerdas que se despliegan: restricción unilateral (solo tensión) y piso.
- Partículas con textura circular.
- Márgenes seguros: títulos en y < 220 y HUD en y > 1000; los rótulos deben leerse en un celular.

## Voz, música y sonido
- Si hay narración: cálida y precisa, 125–145 palabras por minuto, sin duplicar el texto en pantalla. Exporta un clip por escena. La música baja (ducking) bajo la voz.
- Si la voz la graba una persona, entrega un guion con el segundo de entrada de cada frase dentro de su escena y verifica el ritmo (palabras por minuto) de cada frase.
- Música compuesta por código (numpy/scipy), original y sin licencias, con la intensidad ligada a la energía de cada escena.
- Efectos sincronizados con los eventos de la física exportados, no con tiempos a ojo.
- Masteriza a −14 LUFS y −1 dBTP con loudnorm en 2 pasadas. **Vuelve a medir después del AAC** y baja ~0,9 dB si hace falta.
- Claude no puede escuchar: valida con espectrograma, RMS por escena, pico y DC, y pide al usuario que escuche.

## Construcción y render
- 1920×1080 a 30 fps (24 fps si se busca una sensación más cinematográfica).
- Playwright con un navegador Chromium instalado (en Windows, Edge: `channel: 'msedge'`, `--use-angle=d3d11`) → `window.__seek(frame)` → JPEG con calidad 94 → FFmpeg x264 CRF 17; 4 trozos en paralelo; mezcla final del audio con `+faststart`.
- Las capturas JPEG llegan en rango completo: convierte con `scale=in_range=pc:out_range=tv,format=yuv420p` y confirma con ffprobe que el resultado sea `yuv420p` (no `yuvj420p`).
- Los servidores en segundo plano tienen límite de tiempo: renderiza desde un build o con las páginas ya cargadas.

## Revisión
- Hojas de contacto por escena (1 cuadro cada 3–5 s) y en cada corte (justo antes y después): legibilidad, encuadre, superposiciones, saturación. Valida primero la escena central.
- Revisa el video completo una vez **sin sonido** (¿se entiende solo con la imagen?) y otra **solo con el audio** (¿la música y los efectos acompañan la acción?).
- Con ffprobe: duración, resolución, fps, formato de píxel y que el audio dure lo mismo que el video; extrae cuadros del MP4 final.

## Entregables
- Antes de construir: concepto, sistema visual, guion con tiempos, mapa de transiciones y plan de sonido.
- Después: **la animación completa y funcionando**, no solo un concepto o una maqueta.
- El código va fuera de carpetas sincronizadas en la nube (OneDrive, Dropbox, iCloud), por ejemplo en `~/dev/<proyecto>`. Los entregables van a `entregables/`: MP4 + `html/index.html` + `music.mp3`.
- El HTML interactivo tiene play/pausa, selector de capítulos legible (texto claro sobre fondo oscuro, o al revés), volumen y pantalla completa, sin desborde horizontal a 375 px.
- Pregunta antes de publicar algo en internet (Artifacts de Claude u otras plataformas).
