---
name: integrar-asset-generado
description: Cómo traer al juego un personaje que se generó con IA en el servicio de pixel art (por su MCP o a mano en su web) — encontrarlo, confirmar con la persona cuál es cuál, etiquetarlo, registrarlo en los datos del juego y animarlo. Úsala siempre que se diga que ya hay personajes creados y hay que «meterlos», «asignarlos», «ponerles las etiquetas» o «animarlos», aunque no se diga «integrar». NO sirve para encargar dibujos nuevos.
---

# Integrar un asset generado por IA

## Por qué existe

El servicio de generación tiene un servidor MCP que el agente puede usar para
crear, etiquetar y animar personajes. Pero **no todo se puede hacer por MCP**:
algunos tipos de personaje solo se pueden dibujar en la web del servicio, y
luego hay que traerlos al proyecto. Esta skill es ese camino de vuelta.

Todo lo de aquí es **gratis salvo animar**. Listar, consultar y etiquetar no
gastan créditos; animar sí.

## Paso 1 · Encontrar cuáles son

`list_characters` los devuelve del más nuevo al más viejo. Los creados a mano se
reconocen porque **no tienen nombre ni etiquetas**.

Para saber qué dibujo es qué variante hay tres señales, **y no valen lo mismo**:

1. **Mirarlos**, que es la que manda: `get_character(id, include_preview: true)`
   devuelve la imagen.
2. **El tamaño del lienzo**, como pista.
3. **El orden de creación**, como pista.

El tamaño y el orden dicen de qué **encargo** salió un dibujo, no qué **es**.
Así que **enseña las imágenes a la persona y que confirme el reparto antes de
escribir nada**. Colocar una variante donde va otra no da ningún error: solo se
nota cuando alguien las mira juntas.

Cuando llegue una corrección, **escribe el reparto entero**, no «intercambia
estas dos». Una corrección relativa se aplica sobre un estado que puede haber
cambiado, y entonces las correcciones se persiguen unas a otras.

## Paso 2 · Etiquetas

Gratis e inmediato, así que va primero: si algo se tuerce después, al menos la
biblioteca queda ordenada.

```
update_character_tags(character_id, tags: ['<proyecto>', <id>, <variante>, <tipo>])
```

Siempre las mismas y en el mismo orden que usa el resto de herramientas del
proyecto. Sin etiquetas, con cien personajes sin nombre, no se vuelve a
encontrar ninguno.

## Paso 3 · Registrarlo en los datos del juego

Se apunta la imagen de previsualización y el id del servicio en el fichero de
datos. El sprite animado **todavía no**: una entrada sin sprite es justo lo que
el panel muestra como «pendiente de revisar», que es lo que es.

## Paso 4 · Mirarlo antes de pagar

La persona lo revisa en el panel. Este paso no se salta: **animar un dibujo que
se va a tirar es pagar dos veces**.

## Paso 5 · Animar

**Esto cuesta créditos. Pregunta antes, siempre, aunque parezca que ya está
dicho.** Es una regla del proyecto y no tiene excepciones.

Antes de llamar a `animate_character`, mira las animaciones que ya tiene en
`get_character`: el estado del panel vive en memoria y el del servicio no, y es
fácil pagar dos veces la misma.

Al terminar se baja el GIF, se recorta la miniatura, se genera la copia en
espejo para cuando aparece como rival y se pasan las comprobaciones.

## Trampas que ya nos han costado dinero

- **La imagen de un personaje borrado sigue respondiendo.** El fichero se queda
  en el almacenamiento, así que el panel lo pinta tan tranquilo. Antes de dar
  por bueno un id, comprueba que sale en `list_characters`.
- **No hay herramienta para renombrar.** El nombre solo se pone al crear; si
  falta, se cambia a mano en la web. No hace falta buscar la herramienta: ya se
  buscó.
- **El espejo describe el fichero, no es una orden.** En un sprite terminado,
  `espejo: true` dice que el fichero ya está volteado. Leerlo como «voltéalo»
  lo deja mirando al revés.
- **La copia de rival no se hace con CSS**: en Safari de iPhone el giro a veces
  se perdía. Es un fichero aparte, y un test falla si falta.
