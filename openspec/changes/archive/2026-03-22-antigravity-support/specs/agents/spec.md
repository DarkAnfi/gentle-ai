# Agents Specification

## Purpose

Estructura y comportamiento de la detección, configuración e inyección de habilidades para agentes de IA soportados dentro de Gentle AI.

## Requirements

### Requirement: Detección y Configuración de Agente

The system MUST provide an `Adapter` interface to define standard configuration paths (SkillsDir, MCPConfigPath, GlobalConfigDir) per agent.

#### Scenario: Instanciar y configurar Antigravity

- GIVEN the Antigravity agent is targeted by the CLI or TUI
- WHEN Gentle AI resolves its configuration paths
- THEN the system MUST return paths rooted at `~/.gemini/antigravity` (or the OS equivalent)
- AND the system MUST specify the MCP strategy as standard compatible

### Requirement: Registro de Agentes Activos

The system SHALL register all natively enabled agents in a central factory registry so the CLI/TUI can iterate them.

#### Scenario: Carga de lista de herramientas CLI

- GIVEN the application starts and the user wants to list or configure agents
- WHEN `registry.GetAll()` is called
- THEN "antigravity" MUST be included in the map of available agents with its corresponding `Adapter` instance.

### Requirement: Inyección de System Prompt y Simulación Multi-Agent

The system MUST support injecting custom instructions for an agent that lacks subagent creation capabilities.

#### Scenario: Inyección de directivas para Antigravity

- GIVEN Antigravity is a "Single-Agent" architecture
- WHEN the project requests the setup of Spec-Driven Development (SDD)
- THEN the system MUST inject a permanent Orchestrator Role into Antigravity's base rules or System Prompt
- AND the system MUST place all subagent skill files (`sdd-explore`, `sdd-propose`, etc.) into `~/.gemini/antigravity/skills/` so they can be loaded dynamically.
