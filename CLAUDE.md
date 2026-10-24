# Rompecocos — guía del proyecto para Claude Code

Juego de plataformas para navegador, hecho para **Ague Coco** (negocio dominicano de cocos personalizados).
El dueño lo está creando con sus hijas. Responde siempre en **español dominicano sencillo**: el dueño no es programador.

## Qué es el juego
- Tito, un cangrejo, recorre **10 mundos** de República Dominicana (Boca Chica, Las Terrenas, Salto El Limón,
  Bahía de las Águilas, Isla Saona, Cueva de las Maravillas, Zona Colonial, El Morro de Montecristi,
  Los Haitises, Dunas de Baní).
- Camina (◀ ▶), salta (⤒) y lanza cocos arrastrando en la pantalla. Abre cocos contra las rocas para juntar
  agua de coco (ml). Gana al llegar al carrito de Ague Coco con la meta de ml del mundo.
- Enemigos que caminan hacia la izquierda (jaiba, sapo, iguana, araña, gato): se eliminan brincándoles encima
  o con un coco. Ladrones (cigua palmera, murciélago) roban cocos. Aves cazadoras (garza, guaraguao) bajan en picada.
- Arenas movedizas (Dunas de Baní), viento, plataformas que se mueven, erizos, cactus, estalagmitas.
- **5 vidas por mundo** (`LIVES`). Corazones devuelven una vida.
- **Portones del saber**: 1–2 por mundo. Pregunta fácil de Matemáticas, Ciencias Naturales, Ciencias Sociales,
  Lengua Española o Inglés para abrir el paso. Público: niños de primaria. Contenido dominicano y correcto.
- Sonido sintetizado con Web Audio (sin archivos de audio). Récord personal guardado en `localStorage`.
- Marca Ague Coco: stand a mitad de cada mundo, carrito en la meta, cocos personalizados dibujados.
  Logo real en `logo.png` (fondo transparente, `BRAND.logoSrc`): se dibuja encima del techito del stand, en el carrito
  y en el menú de inicio. Colores del logo: verde `#0a6b12`, crema `#fbf1d6`, marrón `#2a1206`, rojo flor `#ee1622`.

## Estructura
Todo vive en **`index.html`** (HTML + CSS + JS en un solo archivo, sin dependencias ni compilación).
Archivos de apoyo para que funcione como app en el teléfono (PWA), publicada en https://aguecoco.github.io/AguaCoco/:
- `manifest.json` (nombre e íconos de la app) · `sw.js` (guarda el juego para jugar sin internet; primero busca
  la versión nueva en internet). Si cambias la lista `FILES` de `sw.js`, sube el número de `VERSION`.
- Íconos de la app: `icono-logo-192.png` y `icono-logo-512.png` (del dueño, en la raíz) e
  `icons/icono-logo-maskable-512.png` (el mismo con margen, para Android). `icons/vista-previa.jpg` (1200×630) es la
  vista previa al compartir el link en WhatsApp; si cambias la imagen, cámbiale también el nombre para que WhatsApp la renueve.
- Las etiquetas `og:*` del `<head>` usan la dirección completa del sitio; si cambia la dirección, actualízalas.

Secciones del script (busca estos comentarios):

| Comentario | Qué contiene |
|---|---|
| `const LIVES`, `const ENEMY` | Vidas y tipos de enemigos |
| `// ---------- mundos ----------` | `THEMES` (colores/escenario de cada mundo) y `LEVELS` (mapa de cada nivel) |
| `// ---------- guardado ----------` | Progreso en `localStorage` (clave `rompecocos.v2`) |
| `// ---------- sonido ----------` | Web Audio y efectos `SFX.*` |
| `// ---------- récord y tabla compartida ----------` | Tabla de puntajes (ver nota abajo) |
| `// ---------- preguntas (portones del saber) ----------` | `SUBJ`, `BANK` (preguntas), `mathQ()` |
| `// ---------- física ----------` | Movimiento de Tito, cocos, rocas |
| `// ---------- enemigos ----------`, `// ---------- aves cazadoras ----------` | IA y dibujo de enemigos |
| `// ---------- portones del saber ----------` | Lógica del cuestionario y dibujo del portón |
| `// ---------- dibujo: escenario ----------` | Fondos, suelo, plataformas, clima |
| `// ---------- marca Ague Coco ----------` | `BRAND`, stand, carrito, cocos personalizados |

### Formato de un nivel (`LEVELS`)
Mundo de 640 de alto; el suelo está en `GY = 560`. Coordenadas x en unidades del mundo.
- `ground: [[x0,x1], …]` tramos de suelo (los huecos son agua o vacío)
- `quick: [[x0,x1]]` arena movediza · `blocks: [[x,y,ancho]]` bloques sólidos
- `plats: [[x,y,ancho,{d,sp}?]]` plataformas (con `{d,sp}` se mueven)
- `rocks`, `cocos: [[x,'normal'|'tierno'|'duro'|'oro',y?]]`, `spikes: [[x,ancho]]`
- `enemies: [x…]`, `wave` (segundos entre enemigos nuevos), `birds: [[x1,x2]]`, `thieves`, `hearts`
- `gates: [x…]` portones del saber (en suelo firme, lejos de rocas, púas y del stand)
- `target` ml necesarios, `goal` x del carrito, `wind`, `signs: [[x,'texto']]`

## Cómo probar
- Abre `index.html` en el navegador (doble clic) o sirve la carpeta: `npx serve .` o `python3 -m http.server`.
- En la consola existe `window.__rc` para pruebas: `__rc.startLevel(n)`, `__rc.state`, `__rc.keys.right = true`,
  `__rc.throwAt(vx, vy)`.
- Prueba siempre en **teléfono vertical (390×800)** y **horizontal (844×390)**: los controles táctiles son clave.
- Si agregas un nivel, comprueba que se puede terminar: que ningún coco caiga al vacío al empezar,
  que los huecos se puedan saltar (salto ≈ 140 de alto, ≈ 200 de largo) y que haya ml suficientes para la meta.

## Reglas
- Mantener un solo archivo y cero dependencias, salvo que el dueño pida otra cosa.
- Textos del juego en español dominicano, cercanos y para niños. Nada de contenido inapropiado.
- Preguntas educativas: fáciles y **verificadas** (fechas patrias, símbolos nacionales, ortografía correcta).
- No usar personajes, logos ni música de otras marcas o juegos.

## Nota sobre la tabla de puntajes
La versión original corría como Artifact de Claude y usaba `window.claude.use('db')` para una tabla compartida.
**Fuera de Claude esa API no existe**: el juego muestra "La tabla compartida no está disponible" y guarda el
récord solo en el dispositivo. Para una tabla real en la web, conectar un servicio como Firebase o Supabase.

## Próximos pasos posibles
1. ~~Poner el logo real de Ague Coco~~ (hecho).
2. ~~Botón "Rétame"~~ (hecho: sale al ganar un mundo; usa el menú de compartir del teléfono o abre WhatsApp).
   Falta: tabla de puntajes compartida en Firebase/Supabase.
3. Publicar como página web (GitHub Pages / Netlify) e instalable (PWA: `manifest.json` + service worker).
4. Separar en archivos (`levels.js`, `questions.js`, …) cuando el proyecto crezca.
5. Empaquetar para Android/iPhone con Capacitor.
