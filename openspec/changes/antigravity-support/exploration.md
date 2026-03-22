## Exploration: Soporte nativo de instalacion para Antigravity

### Current State
Gentle AI soporta múltiples agentes de IA (vscode, codex, gemini, claude, cursor) a través de un patrón de Adaptador en `internal/agents/interface.go`. La configuración de Antigravity (rutas de `.gemini/antigravity/skills`, etc.) está referenciada en el proyecto, pero no existe un adaptador Go en `internal/agents/`. Aparte, existe una limitación arquitectónica clave: **Antigravity no es capaz de crear subagentes customizados de momento**, lo que choca con la arquitectura Multi-Agent orientada a Spec-Driven Development (SDD) predeterminada del ecosistema.

### Affected Areas
- `internal/agents/antigravity/adapter.go` — [NEW] Adaptador principal.
- `internal/agents/antigravity/adapter_test.go` — [NEW] Pruebas unitarias.
- `internal/agents/registry.go` — [MODIFY] Registro del nuevo adaptador.
- `docs/agents.md` — [MODIFY] Documentación.

### Approaches (Resolución Single vs Multi-Agent)
Dado que Antigravity no puede spawnear subagentes, la inyección del rol de orquestador y habilidades debe ajustarse.

1. **Opción A: Multi-Agent Simulado (Orquestador + Carga Dinámica Inline)**
   - El sistema se configura para que Antigravity actúe como Orquestador permanente (Global System Prompt).
   - En lugar de lanzar subagentes para ejecutar las fases (`sdd-explore`, `sdd-propose`), el Orquestador lee el archivo `SKILL.md` correspondiente *inline* usando sus herramientas del sistema de archivos, y se instruye a sí mismo a ejecutar los pasos del subagente sin perder su hilo principal de conversación.
   - Pros: Preserva el ecosistema Gentle AI sin cambiar la forma en que funcionan los archivos `.md` de cada subagente.
   - Cons: Consume el "context window" del agente de forma más rápida porque debe asumir todos los roles en una sola conversación larga interactuando con las memorias (`engram`).

2. **Opción B: Consolidación Single-Agent Monolítica**
   - Inyectar TODO el contexto (todos los `.md` de fases de SDD) directamente al *System Prompt* de Antigravity o usar un archivo `AGENTS.md` (System Rules) que lo abarque todo.
   - Pros: El agente no tiene que buscar o leer dinámicamente qué hacer.
   - Cons: Un prompt global gigantesco, muy ineficiente y que rompe el principio de modularidad que Gentle AI intenta promover.

### Recommendation
**Opción A (Multi-Agent Simulado):** Crear el adaptador para que inyecte `sdd-orchestrator.md` como la regla global base (System Protocol). Luego, Antigravity orquestará las demás habilidades localizándolas en `~/.gemini/antigravity/skills` interactuando bajo un mismo proceso ("Single Agent", pero simulando arquitecturas "Multi-Agent" por lectura dinámica local).

### Risks
- Peligro de "Context Bloat": Todo ocurrirá en la misma sesión, exponiendo al agente a límites de ventanas y olvidos si no es cuidadoso usando MCP Engram.

### Ready for Proposal
Yes. Elevaremos la Opción A a la propuesta.
