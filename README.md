# Sancor Invaders

Una parodia del **Space Invaders** clásico (Taito, 1978) donde los invasores son los shows de drones que se vieron en la cancha: primero el cartel luminoso de **Sancor Seguros** y después el retrato en drones del **"Chiqui" Tapia**. Para defenderte tenés un cañón albiceleste escondido detrás de **tres estrellas doradas**, una por cada Mundial ganado por Argentina, y la nave misteriosa que cruza arriba es la **Copa del Mundo**.

Es un solo archivo HTML, sin dependencias ni instalación: se abre en cualquier navegador, en compu o en celular.

> Parodia sin fines comerciales. No tiene relación con Sancor Seguros, la AFA ni la FIFA.

## Cómo jugar

| Acción | Teclado | Celular |
| --- | --- | --- |
| Moverse | ← → (o A / D) | botones ◀ ▶ |
| Disparar | Espacio (o ↑ / W) | botón FUEGO |
| Empezar | Enter | tocar la pantalla |
| Pausa | P o Esc | tocar la pantalla |
| Sonido | M | botón SONIDO |

### Niveles

- **Nivel 1, Sancor Seguros:** el cartel de drones con el emblema a la izquierda y "SANCOR / SEGUROS" en dos líneas, inclinado como en la foto original. Cada disparo voltea los drones que toca. Cada letra aguanta 3 impactos y el emblema 5.
- **Nivel 2, El Chiqui:** el retrato en drones (unos 1150 puntos), dividido en bloques como los aliens del original. Cada disparo tira un bloque entero.
- Desde ahí los niveles se alternan, cada vez más rápidos y arrancando más abajo.

### Puntos

| Objetivo | Puntos |
| --- | --- |
| Cada impacto en el cartel o el retrato | 10 |
| Terminar una letra de SANCOR | +30 |
| Terminar una letra de SEGUROS | +20 |
| Terminar el emblema | +150 |
| Bloque de la cara del Chiqui | +10 |
| La Copa del Mundo | 50, 100, 150 o 300 |

Se mantienen los detalles del arcade: la formación marcha al ritmo de cuatro notas graves y se acelera a medida que pierde drones; las estrellas se van rompiendo con cada disparo (y con los drones que las atraviesan); hay vida extra a los 1500 puntos; y está el truco clásico de la nave misteriosa: el disparo número 23, y después cada 15, vale 300 puntos. El récord queda guardado en el navegador.

## Publicarlo con GitHub Pages

1. En el repositorio, entrá a **Settings → Pages**.
2. En **Source** elegí **Deploy from a branch**, rama `main` y carpeta `/ (root)`.
3. Guardá. En un minuto el juego queda en `https://<tu-usuario>.github.io/<nombre-del-repo>/`, y cualquiera con ese link puede jugar.

Para probarlo en tu compu alcanza con abrir `index.html` con doble clic.

## Cómo está hecho el código

Todo vive en `index.html`: el CSS arriba, el canvas en el medio y un script en JavaScript puro (sin librerías) abajo. El único recurso externo es la tipografía *Press Start 2P* de Google Fonts; si no carga, se usa una monoespaciada.

El juego dibuja en un `<canvas>` con una resolución lógica de **448 × 512** (el doble de la pantalla original, de 224 × 256) y lo escala para que entre en la ventana. El script está ordenado en secciones marcadas con `// ----------`:

| Sección | Qué hace |
| --- | --- |
| **Drone-dot shapes** | Arma las letras con una grilla de 5 × 7 (`GLYPHS`) y el emblema circular (`logoDots`). Cada punto es un "drone": un círculo con brillo (`shadowBlur`) prerenderizado en un canvas chico (`makeDot`) para dibujarlo rápido cientos de veces por cuadro. En el segundo cuadro de animación cada drone se corre un poquito (`jitter`), como si flotara con el viento. |
| **Mystery ship** | La Copa del Mundo, en pixel art de 22 × 64 calcado de una foto (`TROPHY` + `TROPHY_COL`), con un brillo que la recorre. |
| **The sign / Level 2** | `buildSancor()` y `buildTapia()` devuelven la formación del nivel como una lista de "invasores", cada uno con sus drones (`dots`), cuántos impactos aguanta (`hits`), su fila y su columna. El retrato del Chiqui (`TAPIA`) es una lista de coordenadas y colores; `buildTapia()` lo corta en bloques de 18 × 18 píxeles. |
| **Three gold stars** | Las estrellas son una máscara de celdas de 2 × 2 píxeles (`STAR_MASK`) generada con un test de punto dentro de un polígono, sombreada por facetas para que se vean con relieve. Los impactos borran celdas con `erode()`. |
| **Audio** | Todos los sonidos se sintetizan con la Web Audio API (osciladores y ruido filtrado): la marcha, el disparo, las explosiones y la sirena de la Copa. No hay archivos de audio. |
| **Game state / Update** | El bucle corre a 60 actualizaciones por segundo con paso fijo (`loop` → `update`). Estados: `title`, `playing`, `paused`, `dying`, `clear` y `gameover`. La formación avanza un paso cada `interval` cuadros, que depende de la fracción de drones que quedan vivos. Disparan solo los invasores que no tienen a otro abajo en su misma columna, y casi la mitad de las veces apuntan a tu posición. |
| **Draw** | Dibuja estrellas de fondo, la formación, la Copa, las estrellas doradas, el cañón, los disparos, las partículas (los drones que caen) y el HUD. |
| **Input** | Teclado y botones táctiles (eventos `pointer`). El juego se pausa solo si cambiás de pestaña. |

### Cómo se calcaron las imágenes

- **El retrato del Chiqui:** se tomó la foto del show de drones y se buscaron los máximos locales de brillo (cada drone es un punto de luz). Eso dio unas 1150 coordenadas. Cada drone se clasificó como cálido (cara, naranja) o frío (pelo y traje, blanco y azul), y en dos intensidades, para respetar las sombras del retrato.
- **La Copa:** la foto se recortó, se redujo a 22 píxeles de ancho y se cuantizó a una paleta de dorados. Los píxeles verdosos de la base se pintaron con el verde malaquita de las bandas.

## Ideas para seguir

- Pantalla de récords con iniciales, como en los arcades.
- Que el cartel tire "pólizas" o "cuotas" en vez de balas.
- Modo para dos jugadores alternados (SCORE<2>).
