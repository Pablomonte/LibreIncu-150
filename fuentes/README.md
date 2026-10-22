# Fuentes del manual

El manual publicado está en `docs/index.md`.

## Texto y fotografías

`manual-montaje-original.md` conserva el adjunto proporcionado por el usuario sin modificaciones. Las imágenes se extrajeron de las URLs de HackMD incluidas en ese adjunto. `imagenes.json` registra la URL original, el nombre descriptivo y el SHA-256 de cada archivo. Las fotografías se conservan sin retoques ni reconstrucción.

## CAD utilizado para ilustraciones

Archivo: `LibreIncu_Rediseno_Despiece.FCStd`.
SHA-256: `5ab180d757550a3b983e3bd64d6d23e9dcaabd0f47e3b2760ac321a8220ceab8`.

Se conservó una copia del documento utilizado. El archivo original se abrió para lectura y no se guardó ni modificó. Las vistas muestran componentes existentes, sin reconstruir piezas. Se ajustaron la cámara y los colores para facilitar su lectura y se extrajo el área visible de cada captura, quitando los márgenes blancos sin retocar la geometría.

Brief de las vistas: unidades del documento en mm; X es ancho, Y profundidad, Z altura. La cámara y la iluminación solo afectan las capturas. Salidas: bandejas con ejes y soportes; husillo con KP08 y acople. Validación: selección por nombres de objetos, revisión visual de las capturas y presencia de las advertencias sobre piezas provisionales. No se generan ni modifican STEP o mallas.

El FCStd contiene dimensiones y posiciones provisionales del carro, finales de carrera, cunas y vínculo. No se trasladan esas cotas al manual como medidas de fabricación. Para los detalles del mecanismo se conserva la fotografía del equipo real. El deflector se documenta con las fotografías originales y las medidas confirmadas, sin utilizar la posición guardada del deflector en el CAD como instrucción de montaje.

Para regenerar las capturas con FreeCAD y su interfaz gráfica:

```bash
LIBREINCU_FCSTD="$PWD/fuentes/LibreIncu_Rediseno_Despiece.FCStd" \
LIBREINCU_RENDER_DIR="$PWD/docs/img/cad" \
FreeCAD scripts/render_manual.FCMacro
```

## Aclaraciones confirmadas por el usuario

- Deflector: desarrollo de 225 mm; 470 mm de ancho útil; pestañas de 15 mm por lado; 500 mm de ancho total.
- Utilizar vistas CAD para la disposición general y fotos reales para los detalles que permanecen provisionales en el CAD.
- Relé de la bomba: puede elegirse IN1 o IN2; la entrada controlada por Hum/GPIO17 y la salida NO utilizada por la bomba deben pertenecer al mismo canal.

## Criterios de corrección

- Las conexiones J11, J6 y J9 se transcriben de los rótulos de las fotografías de la fuente; VCC de J6 (3,3 V) se distingue de JVCC del módulo de relés (5 V).
- Se eliminó la segunda aparición de la misma foto del step-down.
- Se reemplazaron los títulos genéricos de las imágenes por descripciones y pies de figura. La fotografía del mecanismo se identifica como conjunto; los planos KP08 y KFL08/KL08 se identifican por separado.
- Los ángulos del deflector se expresan como inclinación principal y pliegues, siguiendo la tabla original, sin atribuir todos los ángulos a planos absolutos respecto de la horizontal.
- Se retiraron del sitio los renders, modelos y manifiestos de la mecánica anterior. Las fotografías de referencia y las nuevas capturas quedan como únicos recursos gráficos del manual.
