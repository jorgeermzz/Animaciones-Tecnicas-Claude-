# Videos técnicos animados con Claude Code

Una plantilla de instrucciones (`CLAUDE.md`) para que [Claude Code](https://claude.com/claude-code) convierta un documento técnico (informe, manual, norma) en un **video animado por código**. Produce un MP4, la música original en MP3 y una página HTML interactiva con capítulos.

Con esta plantilla se hizo el resumen animado del *Informe de Planeamiento Operativo Eléctrico, tercer trimestre 2026* de XM: [ver el video en LinkedIn](https://lnkd.in/p/eKpPvmTW).

## Qué hace la plantilla
- Obliga a un orden de trabajo: concepto → gancho → guion con tiempos → transiciones → sonido → construcción → revisión.
- Exige fidelidad al documento: cada texto lleva su numeral (§) y no se inventan cifras.
- Define un motor web determinista: el mismo código produce el MP4 y el HTML.
- Fija controles de calidad: hojas de contacto, revisión sin sonido, sonoridad a −14 LUFS y −1 dBTP, y comprobaciones con ffprobe.

## Cómo usarla
1. Crea una carpeta para tu proyecto y pon ahí el documento fuente (por ejemplo, `manual.pdf`).
2. Copia `CLAUDE.md` en esa misma carpeta. Si quieres, ajusta la idea central, la audiencia, la duración y el idioma.
3. Abre la carpeta con Claude Code y escribe un prompt como este:

```text
Crea un video que resuma en 4 a 6 minutos el documento manual.pdf.
Lee CLAUDE.md y el documento; si te falta contexto de mi parte, pregúntame.
Los entregables son el MP4, el MP3 con la música y un HTML para verlo localmente.
Asegúrate de que el texto del selector de capítulos sea visible.
```

Claude entrega primero el concepto y el guion (en `plan_produccion.md`), después construye la animación y, al final, la revisa.

### Prompts útiles para después
- `Agrégale un gancho al inicio siguiendo las buenas prácticas para resúmenes técnicos.`
- `Dame un guion para grabar la voz en off, con los tiempos de cada frase.`
- `Haz una hoja de contacto de la escena X y corrige los textos que se superpongan.`

## Requisitos
- Claude Code.
- Node.js 20 o superior.
- Python 3 con `numpy` y `scipy` (para la música).
- FFmpeg en el PATH.
- Un navegador Chromium instalado (Edge o Chrome); Playwright lo usa para renderizar.

Claude instala las dependencias del proyecto (Three.js, Matter.js, Vite y Playwright) cuando las necesita.

## Buenas prácticas
- Usa documentos que puedas compartir. Si el documento es confidencial, no publiques el video ni los archivos generados.
- Revisa las cifras del video contra el documento antes de publicarlo. La plantilla le pide a Claude que cite el numeral de cada dato para que puedas verificarlo.
- Claude no puede escuchar el audio: escúchalo tú antes de publicar.
- El logo y la paleta de marca los aportas tú; la plantilla no los recrea.

## Licencia MIT 
