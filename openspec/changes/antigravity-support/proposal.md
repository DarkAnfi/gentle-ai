# Proposal: Soporte nativo de instalacion para Antigravity

## Intent

Integrar Antigravity al ecosistema Gentle AI, facilitando inyectar configuraciones localmente (`~/.gemini/antigravity/`). Debido a que Antigravity actualmente no soporta instanciación explícita de subagentes (es un solo hilo), el ecosistema debe configurarse para usar un esquema "Single-Agent" que simula un "Multi-Agent" a través de la lectura dinámica de habilidades (skills).

## Scope

### In Scope
- Adaptador `agents.Adapter` para Antigravity.
- Inyección del rol **Orquestador SDD** en las reglas base del agente (System Prompt).
- Configuración para inyectar todas las fases SDD (`sdd-explore`, `sdd-propose`, etc.) como *Skills* independientes en la carpeta `/skills` local de Antigravity.
- Implementación de un catálogo (`AGENTS.md`) para decirle a Antigravity cómo cargar dichos skills manual e "inline" (simulando los subagentes).

### Out of Scope
- Framework nativo para multi-agentes dentro del core de Antigravity.
- Modificación de otros agentes.

## Approach

Implementaremos la Interfaz `agents.Adapter` en `internal/agents/antigravity`. Su ruta base para la inyección de sistema se situará en `~/.gemini/antigravity`.
Estratégicamente emplearemos la **Opción A (Multi-Agent Simulado)**: 
1. **System Prompt**: Se le inyectará el rol "Orchestrator" indicándole su limitación: delegar fases mediante la lectura e interpretación de recursos en `/skills`.
2. **Skills Path**: Todos los subagentes de SDD se instalan físicamente en el entorno de Antigravity. Este actuará tomando distintos roles en una sola conversación a medida que accede a estos archivos.

## Affected Areas

| Area | Impact | Description |
|------|--------|-------------|
| `internal/agents/antigravity/adapter.go` | New | Adaptador principal (maneja la inyección simulada) |
| `internal/agents/antigravity/adapter_test.go` | New | Pruebas unitarias |
| `internal/agents/registry.go` | Modified | Inicialización y registro global |
| `docs/agents.md` | Modified | Documentar el formato de ejecución simulado en Antigravity |

## Risks

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| Límite de la ventana de contexto de Antigravity alcanzado prematuramente (Context Bloat). | High | El adaptador requerirá instruir obligatoriamente el uso de `engram` (MCP) para grabar estados y liberar contextos innecesarios. |

## Rollback Plan

Revertir la creación del paquete en `internal/agents/antigravity` y sacar su configuración de `registry.go`.

## Dependencies
- Model Context Protocol (MCP) Engram (Obligatorio para compensar el límite de "Single Agent" sin desbordamiento de contexto).

## Success Criteria
- [ ] Antigravity es provisionado por Gentle AI con los skills e instrucciones base en `~/.gemini/antigravity`.
- [ ] La instrucción inyectada a Antigravity le pide correctamente cargar delegaciones desde `/skills` ante requerimientos SDD.
