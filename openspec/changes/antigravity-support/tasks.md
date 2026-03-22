# Tasks: Soporte nativo de instalacion para Antigravity

## Phase 1: Foundation (Adapter Core)

- [x] 1.1 Create `internal/agents/antigravity/adapter.go` with the `antigravityAdapter` struct.
- [x] 1.2 Implement the `agents.Adapter` interface paths (`GlobalConfigDir`, `SkillsDir`, `SettingsPath`, `MCPConfigPath`) pointing to `$HOME/.gemini/antigravity`.
- [x] 1.3 Implement `Detect()` to check if the `~/.gemini/antigravity/` folder exists or fallback gracefully.
- [x] 1.4 Implement the System Prompt resolution (Opción A) pointing to a simulated multi-agent base configuration.

## Phase 2: Integration & Wiring

- [x] 2.1 Modify `internal/agents/registry.go` to inject `antigravity.New()` into the `GetAll()` map.

## Phase 3: Testing & Verification

- [x] 3.1 Create `internal/agents/antigravity/adapter_test.go` to test interface compliance and logic (Given/When/Then scenarios from `spec.md`).
- [x] 3.2 Run `go test ./...` specifically verifying `internal/agents/...` to ensure nothing breaks.

## Phase 4: Documentation

- [x] 4.1 Update `docs/agents.md` to add Antigravity to the supported agents list and explain its Single-Agent schema.
