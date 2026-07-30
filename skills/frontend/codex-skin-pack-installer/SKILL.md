---
name: codex-skin-pack-installer
description: Install, validate, apply, switch, and restore Codex desktop skin packs. Use when a developer wants to customize Codex safely without modifying app files or leaking private workspace screenshots.
---

# Codex Skin Pack Installer

## Purpose
Help a developer install and test public-safe Codex desktop skin packs with a reversible workflow.

## Inputs to request
- Operating system and whether the official Codex desktop app is installed.
- Desired skin slug, for example `caishen-readable` or `mythic-guardian-noir`.
- Whether they want a temporary preview, a persistent apply, or a full restore.

## Workflow
1. Start with safety: confirm this is an unofficial skin pack, avoid private screenshots, and explain the restore path before applying anything.
2. Prefer the maintained install route:
   ```bash
   npx skills add ChannelerH/codex-skin-packs --skill codex-skin-pack-installer --global --agent codex --yes
   ```
3. Ask Codex to use the installed skill for the requested skin:
   ```text
   Use $codex-skin-pack-installer to install the caishen-readable Codex skin pack and tell me how to restore the default theme.
   ```
4. Verify the skin package before applying it: check that `theme.json`, assets, and restore guidance are present, and that the pack does not contain private workspace screenshots.
5. Apply or preview the skin only after verification, then confirm the user can still read sidebar text, task output, diffs, and composer input.
6. If the user reports readability, persistence, or restart issues, switch to the previous known-good skin or restore the default theme first, then debug.

## Output
- Exact install command.
- The selected skin slug and source URL.
- Verification result and any safety warnings.
- Apply/preview status.
- Restore command or plain-language restore steps.

## Quality bar
- Do not claim OpenAI affiliation.
- Do not publish or reuse private Codex screenshots.
- Prefer reversible changes and one-command restore paths.
- Treat readability as a hard requirement, not a visual preference.
