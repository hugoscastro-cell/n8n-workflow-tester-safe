# n8n-workflow-tester-safe

Safe MCP server + CLI for testing, inspecting, and operating n8n workflows with a deliberately constrained scope.

## Why this exists

Most n8n tooling either focuses on raw REST access or tries to become a fully autonomous workflow editor. This project takes a stricter path:

- **test workflows safely**
- **inspect executions and traces**
- **query node types/catalog**
- **perform basic workflow CRUD and graph edits**
- **avoid credentials management**
- **avoid destructive backup/restore flows**
- **avoid LLM auto-fix loops**

The goal is to give agents and operators a practical MCP surface for n8n without turning it into an unsafe all-powerful control plane.

## Current scope

### Included
- webhook and manual execution testing
- output evaluation/scoring
- workflow summary lookup
- create/update/delete workflow
- add node to workflow
- connect nodes
- list node types
- get node type details
- list executions
- get execution details
- execution trace summary
- local node catalog search/suggestions

### Explicitly out of scope
- credentials creation/update/export
- secret management
- destructive restore flows
- autonomous LLM repair loops

## Architecture

This repo exposes two interfaces:

1. **MCP server** over stdio for agent/tool integration
2. **CLI** for local test runs from JSON config

Core modules:
- `src/index.ts` — MCP server and tool registration
- `src/cli.ts` — local CLI runner
- `src/n8n-client.ts` — n8n REST client
- `src/evaluator.ts` — scoring/evaluation layer
- `src/catalog.ts` — imported node catalog helpers

## Installation

```bash
npm install
npm run build
```

## Environment

Create a `.env` file from `.env.example`:

```bash
cp .env.example .env
```

Expected values:

```env
N8N_BASE_URL=http://127.0.0.1:5678
N8N_API_KEY=your_api_key_here
N8N_DEFAULT_TIMEOUT_MS=30000
```

## CLI usage

Run all test payloads from a config file:

```bash
node dist/cli.js --config ./workflows/example.json
```

Run a single payload:

```bash
node dist/cli.js --config ./workflows/example.json --payload happy-path
```

## MCP tools

### Testing
- `test_workflow`
- `evaluate_workflow_result`
- `run_workflow_suite`
- `get_workflow_summary`

### Workflow operations
- `create_workflow`
- `update_workflow`
- `delete_workflow`
- `add_node_to_workflow`
- `connect_nodes`

### n8n introspection
- `list_node_types`
- `get_node_type`
- `list_executions`
- `get_execution`
- `get_execution_trace`

### Catalog helpers
- `get_catalog_stats`
- `search_nodes`
- `list_triggers`
- `validate_node_type`
- `suggest_nodes_for_task`

## Example workflow test config

See `workflows/example.json`.

The two execution modes are:

- `webhook` → call an n8n webhook path or full URL
- `execute` → call `POST /api/v1/workflows/:id/execute`

## Safety model

This project is intentionally opinionated.

### Principles
- no credentials management
- no hidden mutations
- no autonomous repair behaviour
- small, inspectable surface area
- clear separation between testing and control operations

### Important note
This tool can still mutate workflows if you call:
- `create_workflow`
- `update_workflow`
- `delete_workflow`
- `add_node_to_workflow`
- `connect_nodes`

Use it with the same care you would use the n8n REST API.

## Repo structure

```text
src/                  TypeScript source
catalog/              Imported node catalog assets
workflows/            Example test configs
dist/                 Build output
```

## Roadmap

- richer workflow diffing
- safer patch operations instead of full replace updates
- better evaluation presets
- exportable reports
- optional read-only mode

## Status

Working MVP.

Built and validated locally with:
- `npm install`
- `npm audit`
- `npm run build`

## License

No license specified yet.
