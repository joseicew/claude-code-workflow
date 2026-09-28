---
name: verificar-en-el-navegador
description: Cómo comprueba el agente él solo un cambio que se ve en pantalla —levantar el servidor, conducir el navegador integrado, medir y hacer captura— y cómo diagnosticar un «no se guarda» o «no se ve» midiendo antes de tocar código. Úsala siempre que un cambio se vea en el panel o en el juego, y siempre que algo «no funciona», «no se guarda», «no aparece» o «sale mal».
---

# Verificar en el navegador

## La regla

**No se pregunta a la persona si funciona: se mira.** Un cambio visual no está
hecho hasta que el agente lo ha visto funcionar y puede enseñar la captura.

## Cómo

1. **Levantar el servidor** con la vista previa integrada, no con la terminal:
   así el agente puede leer los registros y conducir la pestaña.
2. **Leer antes que mirar.** `read_page` y `find` devuelven la estructura con
   referencias; son más fiables y baratos que interpretar una captura.
3. **Rellenar con `form_input` y una referencia**, no tecleando: escribir con
   `type` en los buscadores se pierde a menudo.
4. **Revisar la consola y la red** después de cada acción.
5. **Mirarlo en el tamaño real.** Aquí se juega en un móvil en horizontal:
   812×375. Nada se sale por abajo y el botón de cerrar siempre se ve.
6. **Captura como prueba** al terminar.

## Cuando algo «no se guarda»

Antes de tocar código, **mide**. El fallo casi nunca está donde parece:

1. **¿Está viva la API?** Mira si el puerto escucha.
2. **¿Qué contesta?** La pestaña de red o un `curl`. Un 400 dice que el dato no
   vale; un 409, que el identificador ya existe; un 403 fue una vez CORS.
3. **¿Es la página vieja?** El servidor de desarrollo recarga solo; el que sirve
   la versión construida no se reconstruye.
4. **¿Dónde guarda esa pantalla?** No todas van a la base: alguna guarda en el
   almacenamiento del navegador, y eso no viaja a nadie más.

## Trampas

- **La recarga en caliente devuelve el panel a la primera pestaña.** Después de
  editar hay que volver a la pestaña y volver a filtrar antes de mirar.
- **Al quitar algo, la caché del servidor puede seguir sirviéndolo.** Si borras
  y lo sigues viendo, reinicia el servidor antes de buscar el fallo en el código.
- **Cuidado con los botones de borrar.** Una vez se borró un dato real probando
  el botón, por pulsar la última ficha sin mirar cuál era. Comprueba **qué** vas
  a borrar antes de pulsar, y si estás probando el borrado, crea tú algo que
  borrar.
- **Las pruebas, en silencio.** Si la pantalla suena, se silencia la página
  antes de pulsar, sin tocar el ajuste de sonido del juego.
