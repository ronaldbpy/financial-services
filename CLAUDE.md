# Claude for Financial Services

## Qué es
Plugins y Managed Agent templates para workflows de servicios financieros (IB, equity research, PE, wealth management). Cada agente corre como plugin de Cowork o como Managed Agent via API — misma fuente, dos wrappers.

## Cómo se levanta
```bash
# Validar manifests antes de commitear (obligatorio)
python3 scripts/check.py

# Deploy de un managed agent
export ANTHROPIC_API_KEY=sk-ant-...
scripts/deploy-managed-agent.sh <slug>   # ej: gl-reconciler

# Sincronizar skills vertical-plugins → agent-plugins
python3 scripts/sync-agent-skills.py
```

## Stack
- Markdown + YAML (sin build step)
- Python 3 (scripts de validación y deploy)
- FastAPI + uvicorn + PyJWT (bootstrap Microsoft 365)
- Claude Managed Agents API (`/v1/agents`)

## Estructura
```
plugins/agent-plugins/<slug>/     — agentes nombrados, self-contained
plugins/vertical-plugins/<v>/     — fuentes de skills, commands, MCPs por vertical FSI
plugins/partner-built/            — LSEG, S&P Global
managed-agent-cookbooks/<slug>/   — agent.yaml + subagentes + steering examples
scripts/                          — check.py, deploy-managed-agent.sh, sync-agent-skills.py
claude-for-msft-365-install/      — admin tooling para el add-in de Microsoft 365
```

## Reglas de este proyecto
- Editar skills SIEMPRE en `vertical-plugins/`, luego `sync-agent-skills.py`. Nunca editar directo en `agent-plugins/<slug>/skills/` — `check.py` lo detecta como drift y falla CI.
- Correr `python3 scripts/check.py` antes de cada commit. Valida manifests, referencias cross-file y drift de skills.
- Archivos `.ps1` deben ser ASCII puro. Sin em dash (`—`) ni comillas tipográficas — PowerShell 5.1 en Windows los convierte a mojibake que rompe el parser. Usar `--` no `—`.
- El campo `name` en marketplace es inmutable una vez publicado. Renombrar UI: usar `displayName`. Rename real: agregar entrada en `renames` map de `marketplace.json`.
- Pre-commit hook (instalado por `check.py`) hace patch-bump automático de `version` en `plugin.json`. No forzar versiones manualmente.

## Cosas que ya intentamos y no funcionaron
- Editar skills directamente en `agent-plugins/` — `check.py` falla por drift. Siempre editar en `vertical-plugins/` primero.
- Caracteres UTF-8 (`—`, `"`, `"`) en `.ps1` — invisible en macOS, fatal en Windows.
