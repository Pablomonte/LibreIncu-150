# Fuentes del manual

El manual publicado está en `docs/`, una página por etapa (orden en `mkdocs.yml`).

## Texto y fotografías

`manual-montaje-original.md` conserva el adjunto proporcionado por el usuario sin modificaciones. Las imágenes se extrajeron de las URLs de HackMD incluidas en ese adjunto. `imagenes.json` registra la URL original, el nombre descriptivo y el SHA-256 de cada archivo. Las fotografías se conservan sin retoques ni reconstrucción.

## Mueble

`mueble-cortes.pdf` es el plano del mueble con cajón superior proporcionado por el usuario el 25/11/2026, sin modificaciones (SHA-256 `226a592268ce3cc338ac445159ce3b76d7c0f422a2d282bd7ffb7545192d5481`). `docs/img/manual/mueble-vistas.png` es su página renderizada a 200 ppp con `pdftoppm`, recortada a las dos vistas; la lista de corte y las medidas de la página *Compra y corte* se transcriben de ese PDF, pasadas de cm a mm.

La cámara mide 464 × 500 × 974. De su ancho salen el deflector de 494 × 215 (464 útiles), los perfiles L de la budinera de 462 (1 de juego contra cada lateral) y la chapa del ventilador de 494 × 227. La cuna queda a 20 del lateral opuesto al husillo y a 36 del lateral del husillo; los muñones miden 22 y 38 y terminan a 1,2 de cada lateral.

El cajón aloja el tablero eléctrico y de control, el motorreductor SP 8-18 vertical y la caja estanca de la bomba (98 × 169) y la electroválvula de 12 V. Se abre levantando la tapa superior, con dos bisagras atrás; la puerta no cubre su frente. El eje del motor y el acople pasan por un agujero de Ø28 del techo, que tapa la brida.

Los generadores de `Rediseno/` modelan las 8 placas del mueble y el contenido del cajón: la caja del tablero, el motor con su brida y la caja de bomba y electroválvula. El diámetro del motor, su brida, el largo del eje y la caja de bomba son medidas supuestas hasta tener las piezas. `docs/cad/Deflector_Aire.dxf` lo escribe `export_deflector_dxf` de `LibreIncu_Rediseno_Despiece.py`.

## CAD utilizado para ilustraciones

Fuente congelada de la v3: `fuentes/LibreIncu_Rediseno_Despiece.FCStd`, copia sin cambios de `Rediseno/LibreIncu_Rediseno_Despiece.FCStd` (salida de `LibreIncu_Rediseno_Despiece.py` y `fuente_calor.py`).
SHA-256: `e0fe8cd26baf64f2f6fa92c50387fc10a56c6d0adf9bdf3635e779dc3540d27d` (mueble con cajón superior y su contenido; el fondo con los pases del desagote, del cable de alimentación y de la entrada de agua).

El archivo se abre para lectura y no se guarda ni modifica. Las vistas muestran componentes existentes, sin reconstruir piezas. Se ajustan la cámara y los colores para facilitar su lectura y se extrae el área visible de cada captura, quitando los márgenes blancos sin retocar la geometría.

Unidades en mm; X es ancho, Y profundidad desde el fondo, Z altura desde el piso. Salidas STEP en `docs/cad/`:

| STEP | Sólidos | Contenido |
|---|---|---|
| `LibreIncu_Rediseno_Despiece.step` | 242 | Conjunto completo, con el mueble y el contenido del cajón |
| `bandejas-y-soportes.step` | 82 | Bandejas, ejes, cunas y soportes KFL08 |
| `husillo-kp08-acople.step` | 105 | Husillo, KP08, acople y eje del motor |
| `deflector.step` | 9 | Deflector |
| `fuente-de-calor.step` | 13 | Fuente de calor y chapa de impacto, sin el agua de referencia |
| `carro-y-finales-de-carrera.step` | 10 | Carro plegado, finales de carrera y reed |

`scripts/export_manual.py` exporta copias con posiciones globales, incluyendo los giros y desplazamientos de todos los grupos `App::Part`. Se reimporta cada STEP para comprobar geometría válida, sólidos cerrados de volumen positivo, número de sólidos, volumen y límites XYZ de cada pieza contra la fuente en coordenadas globales. Los límites se calculan con `optimalBoundingBox`: el `BoundBox` de un FCStd guardado desde la interfaz sale de la malla de visualización. El volumen se compara con tolerancia relativa de 1e-4, porque las caras B-spline (la budinera) se integran distinto al reimportar aunque la geometría coincide. Para las piezas del deflector se comprueba además la coincidencia por volumen de intersección. `cad-exportacion.json` registra los resultados y los SHA-256 de fuente y salidas; `docs/img/cad/vistas.json` registra el SHA-256 del FCStd usado para las capturas.

Desde la v3, las cotas confirmadas del deflector, la fuente de calor, el carro y los finales de carrera se toman del FCStd. Las fotografías del equipo real se conservan para los detalles que el modelo no muestra (tablero, sensores reed).

Los desarrollos de chapa (`docs/cad/*.dxf`) se copian sin cambios de `Rediseno/`: el deflector y la contraplaca los escriben los generadores del CAD, la chapa ranurada la escribe `planos/fuentes/chapa_ranurada.py` y el carro es un dibujo propio. `scripts/render_desarrollos.py` los dibuja acotados en `docs/img/cad/desarrollo-*.png` (requiere matplotlib, no FreeCAD).

Para regenerar las capturas con FreeCAD y su interfaz gráfica:

```bash
LIBREINCU_FCSTD="$PWD/fuentes/LibreIncu_Rediseno_Despiece.FCStd" \
LIBREINCU_RENDER_DIR="$PWD/docs/img/cad" \
FreeCAD scripts/render_manual.FCMacro
```

Si el macro se ejecuta dentro de una sesión de FreeCAD que debe seguir abierta (por ejemplo, desde el bridge MCP), definir `LIBREINCU_KEEP_OPEN=1` para que no cierre la ventana principal.

Para regenerar los STEP, ejecutar desde la raíz del repositorio con un Python que pueda importar `FreeCAD` y `Part`:

```bash
python scripts/export_manual.py fuentes/LibreIncu_Rediseno_Despiece.FCStd \
  docs/cad --manifest fuentes/cad-exportacion.json
```

Con un AppImage montado, ese Python está disponible como `AppRun python`; definir `PYTHONPATH` apuntando a `usr/lib` dentro del montaje. Después de regenerar las capturas, actualizar el PDF con `python docs/build_doc.py` usando las dependencias indicadas en ese script.

Antes de publicar cambios del exportador, ejecutar `python scripts/test_export_manual.py` con ese mismo Python de FreeCAD. La prueba comprueba transformaciones anidadas y la reimportación STEP, incluyendo un caso donde los límites y el volumen del conjunto ocultan una pieza desplazada. Todos sus archivos se crean en una carpeta temporal.

## Planos

`docs/planos/v3/` tiene los planos de fabricación de la v3 (LI150-V3), PDF y DXF, copiados sin cambios de `Rediseno/planos/`. Los escriben los generadores de `Rediseno/planos/fuentes/` (`todos.py`) con las cotas de `LibreIncu_Rediseno_Despiece.py`, `fuente_calor.py` y `plantillas_perforacion.py`; la lista, el código y las decisiones están en `Rediseno/PLANOS_V3.md`. `docs/planos/v3/corte/` tiene los DXF de corte de cada pieza, que escribe `Rediseno/planos/fuentes/dxf_corte.py`, copiados sin cambios de `Rediseno/planos/corte/`. `docs/img/planos/v3/*.png` son las miniaturas de los planos: la primera página de cada PDF, pasada a PNG de 420 px de ancho con `pdftoppm -scale-to 420`, sin retoques. `LI150-V3-06-04_desarrollo.png` (Figura 7) es el desarrollo de ese plano, recortado de la página pasada a 200 ppp.

## Aclaraciones confirmadas por el usuario

- Deflector: desarrollo de 215 mm, con paños de 115, 60 y 40 mm; ancho útil igual al de la cámara (464 mm); pestañas de 15 mm por lado; 494 mm de ancho total; pliegues de 50° y 20°; paño vertical a 60 mm del fondo y punto más bajo a 300 mm del piso.
- Techo: agujero pasante de Ø28 para el eje del motor y el acople; el acople se aprieta al eje del motor antes de montarlo y la brida tapa el agujero.
- Tapa superior: dos bisagras atrás. Perfiles de chapa en los cantos del mueble y de la puerta.
- Ventilador: corrido 11 mm del centro de la chapa hacia su borde bajo, para que no toque el techo.
- Caja de bomba y electroválvula: desagote afuera, por el fondo.
- Utilizar vistas CAD para la disposición general y fotos reales para los detalles que el CAD no muestra.
- Relé de la bomba: puede elegirse IN1 o IN2; la entrada controlada por Hum/GPIO17 y la salida NO utilizada por la bomba deben pertenecer al mismo canal.

## Criterios de corrección

- Las conexiones J11, J6 y J9 se transcriben de los rótulos de las fotografías de la fuente; VCC de J6 (3,3 V) se distingue de JVCC del módulo de relés (5 V).
- Se eliminó la segunda aparición de la misma foto del step-down.
- Se reemplazaron los títulos genéricos de las imágenes por descripciones y pies de figura. La fotografía del mecanismo se identifica como conjunto; los planos KP08 y KFL08/KL08 se identifican por separado.
- Los ángulos del deflector se expresan como inclinación principal y pliegues, siguiendo la tabla original, sin atribuir todos los ángulos a planos absolutos respecto de la horizontal.
- Se retiraron del sitio los renders, modelos y manifiestos de la mecánica anterior. Los recursos gráficos del manual son las fotografías de referencia, las capturas del FCStd congelado, los desarrollos acotados de los DXF, los planos de la v3 y el plano del mueble.
