# Fuentes del manual

El manual publicado está en `docs/`, una página por etapa (orden en `mkdocs.yml`).

## Texto y fotografías

`manual-montaje-original.md` conserva el adjunto proporcionado por el usuario sin modificaciones. Las imágenes se extrajeron de las URLs de HackMD incluidas en ese adjunto. `imagenes.json` registra la URL original, el nombre descriptivo y el SHA-256 de cada archivo. Las fotografías se conservan sin retoques ni reconstrucción.

## Mueble

`mueble-cortes.pdf` es el plano del mueble con cajón superior proporcionado por el usuario el 25/11/2026, sin modificaciones (SHA-256 `226a592268ce3cc338ac445159ce3b76d7c0f422a2d282bd7ffb7545192d5481`). `docs/img/manual/mueble-vistas.png` es su página renderizada a 200 ppp con `pdftoppm`, recortada a las dos vistas; la lista de corte y las medidas de la página *Compra y corte* se transcriben de ese PDF, pasadas de cm a mm.

La cámara mide 464 × 500 × 974. De su ancho salen el deflector de 494 × 225 (464 útiles), los perfiles L de la budinera de 464 y la chapa del ventilador de 494 × 227. La cuna queda a 20 del lateral opuesto al husillo y a 36 del lateral del husillo; los muñones miden 22 y 38 y terminan a 1,2 de cada lateral.

El cajón aloja el tablero eléctrico y de control, el motorreductor SP 8-18 vertical y la caja estanca de la bomba (98 × 169) y la electroválvula de 12 V. Se abre levantando la tapa superior; la puerta no cubre su frente.

`docs/cad/Deflector_Aire.dxf` se generó con `export_deflector_dxf` de `LibreIncu_Rediseno_Despiece.py` y `CHAMBER_WIDTH = 464`. Los generadores de `Rediseno/` todavía no incorporan el mueble: el FCStd congelado, los STEP, las capturas y la animación se regeneran cuando se actualicen.

## CAD utilizado para ilustraciones

Fuente congelada de la v3: `fuentes/LibreIncu_Rediseno_Despiece.FCStd`, copia sin cambios de `Rediseno/LibreIncu_Rediseno_Despiece.FCStd` (salida de `LibreIncu_Rediseno_Despiece.py` y `fuente_calor.py`).
SHA-256: `497dc19aff6be35a06758810fee3e101a145e313287a78e18de612bf860fbbcf` (carro con agujeros de Ø3 para los tornillos de 2,9 mm de la brida).

El archivo se abre para lectura y no se guarda ni modifica. Las vistas muestran componentes existentes, sin reconstruir piezas. Se ajustan la cámara y los colores para facilitar su lectura y se extrae el área visible de cada captura, quitando los márgenes blancos sin retocar la geometría.

Unidades en mm; X es ancho, Y profundidad desde el fondo, Z altura desde el piso. Salidas STEP en `docs/cad/`:

| STEP | Sólidos | Contenido |
|---|---|---|
| `LibreIncu_Rediseno_Despiece.step` | 225 | Conjunto completo, con paredes de referencia |
| `bandejas-y-soportes.step` | 78 | Bandejas, ejes, cunas y soportes KFL08 |
| `husillo-kp08-acople.step` | 105 | Husillo, KP08 y acople |
| `deflector.step` | 9 | Deflector |
| `fuente-de-calor.step` | 13 | Fuente de calor y chapa de impacto, sin el agua de referencia |
| `carro-y-finales-de-carrera.step` | 6 | Carro plegado y finales de carrera |

`scripts/export_manual.py` exporta copias con posiciones globales, incluyendo los giros y desplazamientos de todos los grupos `App::Part`. Se reimporta cada STEP para comprobar geometría válida, sólidos cerrados de volumen positivo, número de sólidos, volumen y límites XYZ de cada pieza contra la fuente en coordenadas globales. Los límites se calculan con `optimalBoundingBox`: el `BoundBox` de un FCStd guardado desde la interfaz sale de la malla de visualización. El volumen se compara con tolerancia relativa de 1e-4, porque las caras B-spline (la budinera) se integran distinto al reimportar aunque la geometría coincide. Para las piezas del deflector se comprueba además la coincidencia por volumen de intersección. `cad-exportacion.json` registra los resultados y los SHA-256 de fuente y salidas; `docs/img/cad/vistas.json` registra el SHA-256 del FCStd usado para las capturas.

Desde la v3, las cotas confirmadas del deflector, la fuente de calor, el carro y los finales de carrera se toman del FCStd. Las fotografías del equipo real se conservan para los detalles que el modelo no muestra (tablero, sensores reed).

Los desarrollos de chapa (`docs/cad/*.dxf`) se copian sin cambios de `Rediseno/`, salvo el del deflector (ver [Mueble](#mueble)): el deflector y la contraplaca los escriben los generadores, y el carro y la chapa ranurada son dibujos propios. `scripts/render_desarrollos.py` los dibuja acotados en `docs/img/cad/desarrollo-*.png` (requiere matplotlib, no FreeCAD).

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

## Aclaraciones confirmadas por el usuario

- Deflector: desarrollo de 225 mm; ancho útil igual al de la cámara (464 mm); pestañas de 15 mm por lado; 494 mm de ancho total; pliegues de 50° y 20°; paño vertical a 60 mm del fondo y punto más bajo a 300 mm del piso.
- Utilizar vistas CAD para la disposición general y fotos reales para los detalles que el CAD no muestra.
- Relé de la bomba: puede elegirse IN1 o IN2; la entrada controlada por Hum/GPIO17 y la salida NO utilizada por la bomba deben pertenecer al mismo canal.

## Criterios de corrección

- Las conexiones J11, J6 y J9 se transcriben de los rótulos de las fotografías de la fuente; VCC de J6 (3,3 V) se distingue de JVCC del módulo de relés (5 V).
- Se eliminó la segunda aparición de la misma foto del step-down.
- Se reemplazaron los títulos genéricos de las imágenes por descripciones y pies de figura. La fotografía del mecanismo se identifica como conjunto; los planos KP08 y KFL08/KL08 se identifican por separado.
- Los ángulos del deflector se expresan como inclinación principal y pliegues, siguiendo la tabla original, sin atribuir todos los ángulos a planos absolutos respecto de la horizontal.
- Se retiraron del sitio los renders, modelos y manifiestos de la mecánica anterior. Los recursos gráficos del manual son las fotografías de referencia, las capturas del FCStd congelado, los desarrollos acotados de los DXF, el plano 12-02 del INTI y el plano del mueble.
