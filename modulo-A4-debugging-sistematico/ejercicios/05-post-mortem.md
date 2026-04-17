# Ejercicio 5: Post-mortem

## Contexto

Este ejercicio se basa en el resultado del [Ejercicio 4: Debugging cross-stack](04-debugging-cross-stack.md). Si no lo has completado, hazlo primero.

Asume que el bug del ejercicio 4 llegó a producción y afectó a usuarios reales durante 3 días antes de ser detectado.

## Información del incidente

- **Fecha de introducción del bug**: lunes 10 de junio, deploy a las 10:00
- **Fecha de detección**: jueves 13 de junio, 15:30 (un usuario del equipo de soporte reportó que algunos proyectos "desaparecían")
- **Fecha de resolución**: jueves 13 de junio, 17:00
- **Entorno afectado**: producción
- **Commit que introdujo el bug**: el que implementó la funcionalidad de búsqueda de proyectos
- **Commit del fix**: el que corrigió la query

## Tu tarea

### Parte 1: Genera el post-mortem

Usa el template del módulo para generar un post-mortem completo con estas secciones:

1. **Resumen ejecutivo** (2-3 frases)
2. **Timeline** (tabla con horas y eventos clave)
3. **Causa raíz** (explicación técnica)
4. **Impacto** (estima usuarios afectados, datos perdidos si los hay)
5. **Acciones correctivas** (qué se hizo para resolverlo)
6. **Acciones preventivas** (qué se hará para que no se repita)
7. **Lecciones aprendidas**

### Parte 2: Tests de regresión

Escribe 3 tests de regresión que:
- Cubran el bug original
- Cubran variaciones del mismo problema (otros caracteres especiales)
- Fallen con el código original y pasen con el fix

### Parte 3: Actualización del Archivo de Instrucciones

Escribe las líneas que añadirías al archivo de instrucciones del proyecto (`AGENTS.md`, `CLAUDE.md` o equivalente) para que un agente de código no vuelva a introducir este tipo de bug. Piensa en:
- Regla sobre construcción de queries SQL
- Regla sobre tests con caracteres especiales
- Regla sobre validación de inputs

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Post-mortem completo con las 7 secciones |
| **Notable** | Post-mortem + 3 tests de regresión funcionales |
| **Excelente** | Todo lo anterior + actualización del archivo de instrucciones con reglas específicas y accionables |

## Ejemplo de formato esperado

Tu post-mortem debería seguir este formato (pero con el contenido real del incidente):

```markdown
## Post-mortem: [Título descriptivo del incidente]

### Resumen ejecutivo
[2-3 frases que resuman qué pasó, cuánto duró y cuál fue el impacto]

### Timeline
| Fecha/Hora | Evento |
|------------|--------|
| ... | ... |

### Causa raíz
[Explicación técnica: qué código causó el problema y por qué]

### Impacto
[Usuarios afectados, funcionalidad rota, duración, datos perdidos]

### Acciones correctivas
- [x] [Lo que ya se hizo]
- [x] [Lo que ya se hizo]

### Acciones preventivas
- [ ] [Lo que se hará]
- [ ] [Lo que se hará]

### Lecciones aprendidas
1. [Lección 1]
2. [Lección 2]
```

---

[← Ejercicio anterior](04-debugging-cross-stack.md) | [Volver al índice del módulo](../README.md)
