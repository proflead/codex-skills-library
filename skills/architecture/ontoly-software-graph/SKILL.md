---
name: ontoly-software-graph
description: Use Ontoly's deterministic Software Graph and MCP capabilities for architecture review, request tracing, dependency analysis, and impact analysis.
---

# Ontoly Software Graph

## Purpose
Use Ontoly as the source of truth for graph-backed codebase understanding before searching source files directly.

## Inputs to request
- Repository path or current workspace.
- Existing Ontoly artifacts such as `.ontoly/`, `SoftwareGraph.json`, diagnostics, validation reports, or MCP configuration.
- The concrete question: architecture, route trace, dependency owner, impact radius, configuration usage, or graph diagnostics.

## Workflow
1. Check whether an Ontoly graph already exists.
2. If the graph is missing and local graph output is acceptable, run `ontoly build .`.
3. Review graph diagnostics, trust, semantic coverage, framework detection, and validation status.
4. Prefer Ontoly CLI or MCP capabilities for architecture summaries, request tracing, dependency analysis, configuration lookup, and impact analysis.
5. Inspect source files only when Ontoly cannot answer, the graph is incomplete, or the user asks for source-level verification.

## Output
- Relevant graph nodes, edges, packages, routes, diagnostics, or query outputs.
- Clear separation between measured graph facts and inference.
- Confidence based on graph evidence, not guesswork.
- Any fallback source inspection and why it was needed.

## Quality bar
- Do not claim a relationship exists unless Ontoly reports it or the answer labels it as inferred.
- Treat unresolved imports, low trust, missing framework detection, and validation failures as answer limitations.
- Rebuild the graph after meaningful repository changes before answering current-state questions.
