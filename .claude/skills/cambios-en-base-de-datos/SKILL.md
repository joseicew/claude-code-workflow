---
name: cambios-en-base-de-datos
description: El ciclo completo para cambiar la base de datos MySQL del proyecto — escribir la migración, aplicarla, volcar el esquema y los datos, y comprobar — y cómo conectarse sin exponer la contraseña. Úsala siempre que un cambio toque la base, aunque no lo parezca: crear o editar un dato desde el panel, añadir una columna, «créame estos cinco registros», «borra este», «cambia el valor por defecto». Si toca MySQL, esta skill manda.
---

# Cambios en la base de datos

## Por qué existe

La base de datos es la fuente de verdad del contenido, pero el repositorio
guarda **una foto** de ella (`backend/db/schema.sql` y `backend/db/seed.sql`)
para que cualquiera pueda levantarla desde cero. Si un cambio se hace en la base
y no en la foto, el siguiente que arranque el proyecto lo pierde, y nadie se
entera hasta que falta algo.

Por eso **todo cambio que toque la base pasa por el volcado**. Sin excepciones,
también cuando el cambio se hizo desde el panel y no hay migración.

## El ciclo

1. **Migración.** `backend/db/migraciones/NNN-nombre-en-kebab.sql`, con el número
   siguiente al último que haya. Una migración, un propósito.
2. **Aplicarla.** Las migraciones las aplica el script de arranque, **no la API
   al arrancar**. Si no se va a lanzar el proyecto, se aplica a mano y se apunta
   en la tabla de control:
   ```sql
   INSERT INTO migracion_aplicada (nombre) VALUES ('NNN-nombre.sql');
   ```
3. **Volcar.** `scripts/volcar-base.ps1` reescribe `schema.sql` y `seed.sql`.
   **Solo vuelca; no aplica nada.** Confundir los dos scripts es el error más
   común.
4. **Comprobar y hacer commit**, con la migración y el volcado en el mismo commit.

## La contraseña

- Sale **solo** de `$MYSQL_PASSWORD` o de `.env.local`, que está en el
  `.gitignore`.
- **Nunca en un fichero del proyecto y nunca en la línea de órdenes**: los
  argumentos de un proceso los puede leer cualquiera en la máquina y acaban en
  el historial. Se pasa por un `--defaults-extra-file` temporal que se borra al
  terminar:
  ```powershell
  $tmp = New-TemporaryFile
  try {
      "[client]`nuser=root`npassword=$env:MYSQL_PASSWORD" | Set-Content $tmp -Encoding ascii
      & mysql.exe --defaults-extra-file=$tmp --default-character-set=utf8mb4 proyecto -e $consulta
  } finally { Remove-Item $tmp -Force }
  ```
- Para saber si está puesta, **solo** esto:
  ```bash
  [ -n "$MYSQL_PASSWORD" ] && echo puesta || echo ausente
  ```

## Trampas que ya nos han pasado

- **`${MYSQL_PASSWORD:-no}` imprime la contraseña.** `:-` expande el valor
  cuando la variable no está vacía. Parece una comprobación y es un `echo`. Pasó
  dos veces antes de que esta regla existiera.
- **Sin `--default-character-set=utf8mb4` los acentos salen rotos**, y el volcado
  los guarda rotos. El texto queda mal para siempre sin que nada falle.
- **La API cachea el mapeo de entidades.** Tras tocar el esquema o una entidad
  Java hay que reiniciarla, o seguirá escribiendo en columnas que ya no existen.
  Una migración que solo cambia datos no lo necesita.
- **`mysql.exe` no está en el PATH.** Usa la ruta completa de la instalación.
