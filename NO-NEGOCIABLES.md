# Mis 5 no negociables

---

## 1. Subagentes — para los proyectos grandes

No le doy todo a un Claude. Abro varios y hago que cada uno sea experto en un área.

Un subagente es un archivo `.md` en `.claude/agents/`. Solo `name` y `description`
son obligatorios:

```markdown
---
name: revisor
description: Revisa código antes de commitear. Usar después de cada cambio.
tools: Read, Grep, Glob
model: sonnet
---

Sos un revisor de código. Para cada problema que encuentres, explicá
qué está mal, mostrá el código actual, y proponé el arreglo.
```

**Por qué funciona:** cada agente arranca con su propio contexto limpio y sus propias
instrucciones. El que revisa no arrastra las suposiciones del que escribió.

**Cuándo lo uso:** cuando la tarea tiene tres o más partes que no dependen entre sí.
Para un cambio de dos archivos, no vale la pena.

- Los creás también con `/agents`
- Se guardan en `.claude/agents/` (proyecto) o `~/.claude/agents/` (todos tus proyectos)
- Las carpetas se leen recursivamente, así que podés organizarlos en subcarpetas

---

## 2. Skills — para las tareas repetitivas

Lo armo una vez. Cuando toca esa tarea, la llamo y se repite siempre igual.

Una skill es una carpeta con un `SKILL.md` adentro:

```
.claude/skills/mi-tarea/SKILL.md
```

```markdown
---
name: mi-tarea
description: Qué hace y cuándo usarla. Esto es lo que Claude lee para decidir si activarla.
---

# Mi tarea

Los pasos, en orden, como se los explicarías a alguien que entra mañana.
```

**La parte que la gente escribe mal:** la `description`. Es lo único que Claude lee para
decidir si la usa. Si dice "ayuda con documentos", nunca se va a activar en el momento
correcto. Poné los disparadores: *"Usar cuando pida X, Y o Z."*

**Cuándo la armo:** a la tercera vez que explico lo mismo.

---

## 3. MCP — para que sea un asistente de verdad

Conecto Claude con mis herramientas. Deja de ser un chat: pasa a contestar por mí.

Los servidores del proyecto van en `.mcp.json`, en la raíz:

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase@latest"],
      "env": { "SUPABASE_ACCESS_TOKEN": "${SUPABASE_ACCESS_TOKEN}" }
    }
  }
}
```

- `/mcp` te muestra qué está conectado y qué falta autorizar
- **Nunca pongas la clave escrita en el archivo.** Usá variables de entorno, como arriba.
  Ese archivo se sube a git.

---

## 4. Compactar — antes de cada tarea nueva

Cuando el contexto se llena, lo compacto.

| Comando | Cuándo |
|---|---|
| `/compact` | Seguís con lo mismo pero el contexto pesa. Resume y sigue. |
| `/clear` | Cambiás de tema. Borra todo y arranca limpio. |

**La señal para compactar:** cuando Claude empieza a proponerte soluciones del problema
de hace dos horas. Eso no es que "se puso tonto": es que tiene demasiado ruido adelante.

**El error común:** seguir en la misma conversación todo el día. El contexto viejo no es
gratis — ocupa lugar y confunde.

---

## 5. El modelo correcto — caro para lo difícil, barato para lo fácil

| Modelo | Para qué |
|---|---|
| `fable` / `opus` | Arquitectura, decisiones, código nuevo con muchas piezas |
| `sonnet` | El día a día |
| `haiku` | Renombrar, buscar, formatear, tareas mecánicas |

Se cambia con `/model`, o se fija por agente con el campo `model:` en su archivo.

**Lo que casi nadie hace:** poner el modelo en el agente. Un agente que solo busca
archivos no necesita el modelo más caro, y lo va a usar igual si no se lo decís.

---

## Bonus: las tres reglas que van arriba de todo

**1. Commit antes de largarlo.** Si el estado anterior está en git, cualquier cosa se
deshace en un comando.

**2. Pedí plan antes que código.** *"No escribas nada todavía. Decime qué archivos vas a
tocar y qué se puede romper."* Es más barato corregir un plan que un cambio.

**3. Pedí la salida, no el resumen.** *"Corré los tests y pegame lo que devuelve."*
Un "debería funcionar" no es una prueba.
