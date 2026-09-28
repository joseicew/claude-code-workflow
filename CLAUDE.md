# Proyecto de ejemplo

> Versión generalizada del `CLAUDE.md` de un proyecto personal. Es lo que el
> agente lee al empezar cada sesión: poco, concreto y con el motivo de cada regla.

Videojuego por turnos. El **juego es Godot 4** (`godot/`); detrás, Spring Boot +
MySQL, y un panel web de datos en React (`/panel`, puerto 4900).

**Todo el código, los comentarios y los textos de la interfaz van en español.**

## Las skills mandan

Cuando el trabajo caiga en una de estas, léela antes de tocar nada. Están en
`.claude/skills/`:

| Skill | Para qué |
|---|---|
| `cambios-en-base-de-datos` | Migraciones, volcado y acceso a MySQL sin exponer la contraseña |
| `integrar-asset-generado` | Traer al juego un sprite generado por IA |
| `verificar-en-el-navegador` | Comprobar un cambio visual y diagnosticar «no se guarda» |

## Reglas del proyecto

Estas las pone la persona y no se discuten.

- **Los nombres los pone ella.** No inventes nombres de personajes, ataques ni
  mapas. Si falta uno, pregunta o deja el hueco.
- **Pregunta antes de generar dibujos o animaciones.** Cuestan créditos. Vale
  también para rehacer uno que ya existe.
- **Commit por cada avance bueno y verificado**, sin preguntar. Commit no es
  push: `push` solo si lo pide.
- **Lo que se marca en ROJO en una captura es lo importante.** Antes de dar por
  hecha una petición con captura, repasa cada marca y di qué has hecho con ella.
- **Todo se diseña para un móvil en horizontal.** Una ventana nueva se mira a
  812×375 antes de darla por buena: nada se sale por abajo y el cierre siempre
  está a la vista.
- **Los datos del panel se exportan al juego.** Tras tocarlos, se regenera la
  copia de Godot con su script; sin eso, el juego sigue con los de antes.

## Secretos

- La contraseña de MySQL sale **solo** del entorno o de `.env.local` (ignorado
  por git). **Nunca en un fichero del proyecto ni en la línea de órdenes.**
- Para comprobar si está puesta, **solo**:
  `[ -n "$MYSQL_PASSWORD" ] && echo puesta || echo ausente`.
  Nunca `${MYSQL_PASSWORD:-...}`: expande el valor y lo imprime.
- Los tokens de los servicios MCP viven en el entorno, **nunca en el
  repositorio**. No se imprimen jamás.
- **No autentiques nunca en nombre de la persona.**

## Comprobar

```bash
npm run comprobar
```

Es `tsc --noEmit && eslint src && vitest run`. Hay una base de avisos de lint
que ya existía y sirve de referencia: **si el número sube, lo has roto tú.**

## Cosas del entorno que ahorran una hora

- **Las migraciones las aplica el script de arranque, no la API.** El script de
  volcado solo vuelca.
- **El JVM de Spring cachea el mapeo de entidades.** Tras tocar el esquema o una
  entidad hay que reiniciar la API. Una migración de solo datos, no.
- **El repositorio tiene finales de línea mezclados.** Cualquier edición por
  script detecta `\r\n`, normaliza, edita y restaura.
- Para levantar el servidor de desarrollo se usa la vista previa integrada, no
  la terminal.
