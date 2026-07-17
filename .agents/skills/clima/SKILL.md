---
name: clima
description: >
  Consulta el clima actual desde una sola fuente (wttr.in), sin API key ni dependencias.
  Devuelve una línea con ubicación, condición y temperatura.
  Úsala cuando el usuario diga "clima", "tiempo", "qué temperatura hace", "ver el clima"
  o invoque /clima. Acepta una ciudad opcional como argumento (por defecto, autodetección por IP).
---

Consulta el clima usando **únicamente** la fuente `wttr.in`. No uses ninguna otra fuente ni API.

## Cómo consultar

Ejecuta un solo comando y muestra su salida al usuario tal cual:

```bash
curl -s "wttr.in/${CIUDAD:-}?format=3&m&lang=es"
```

- Sin argumento → `wttr.in/?format=3` autodetecta la ubicación por IP.
- Con ciudad → sustituye `${CIUDAD}` por el argumento (ej. `/clima Santiago` → `wttr.in/Santiago?...`).
- `format=3` devuelve una sola línea: `Ubicación: 🌦 +18°C`.
- `&m` fuerza unidades métricas; `&lang=es` pide la condición en español.

## Reglas

- **Una sola fuente**: solo `wttr.in`. Si falla, informa el error; no consultes alternativas.
- Muestra la línea de resultado directamente, sin adornos ni interpretación extra.
- No guardes historial ni escribas archivos.

## Ejecución periódica (cada 2 minutos)

Esta skill está pensada para ejecutarse cada 2 minutos. Para activarla en bucle:

```
/loop 2m /clima
```

Cada disparo vuelve a ejecutar la consulta y muestra el clima actualizado.
Para detener el bucle, cancela el `/loop`.
