---
title: 'VS Code Agent Host and the Copilot Harness'
description: 'Understand the Copilot harness, Agent Host architecture, and sandboxing introduced in VS Code — and how they align VS Code with Copilot CLI and the Copilot app.'
authors:
  - GitHub Copilot Learning Hub Team
lastUpdated: 2026-10-13
estimatedReadingTime: '7 minutes'
tags:
  - vscode
  - agent-host
  - sandboxing
  - platform
relatedArticles:
  - ./github-copilot-app.md
  - ./copilot-configuration-basics.md
  - ./understanding-mcp-servers.md
prerequisites:
  - Basic familiarity with GitHub Copilot in VS Code
---

VS Code's chat experience has historically run inside the main editor process. Starting with the Copilot harness and the underlying **Agent Host** architecture, VS Code agent sessions share the same engine that powers the standalone GitHub Copilot app and Copilot CLI — bringing consistent behavior, sandboxing, and session management across all three surfaces.

This article explains what the Copilot harness and Agent Host are, why they matter, and how to configure them.

## What Is the Copilot Harness?

The **Copilot harness** is a chat "harness" (agent runtime) option in VS Code powered by the **Copilot SDK** — the same SDK that drives the GitHub Copilot app and Copilot CLI. Because it's the same underlying engine, agent behavior and capabilities are now consistent across all three products instead of diverging per-client.

Select the Copilot harness from the harness picker in the chat input. Depending on your VS Code version and enterprise rollout, it may already be the default.

## What Is the Agent Host?

The Copilot harness runs inside a dedicated **Agent Host** process, built on the **Agent Host Protocol (AHP)**. Running agent sessions in a separate process (rather than inline in the editor) enables several capabilities that weren't previously possible:

- **Multi-window connections**: Connect to the same agent session from multiple VS Code windows simultaneously.
- **Sandboxing**: Enforce filesystem and network restrictions on what the agent can access while it works (see below).
- **Consistent session management**: The same session grid, filtering, and worktree cleanup tools used in the Agents window apply whether the session originated in VS Code, the Copilot CLI, or the Copilot app.
- **External session continuity**: Conversations started in the Copilot CLI or Copilot app appear automatically as external sessions in the Agents window as soon as you send the first request — no reload required.

## Sandboxing in the Copilot Agent Host

**Setting**: `chat.agent.sandbox.enabled`

Sandboxing limits what the agent can access on your filesystem and network while it works, reducing the blast radius of model mistakes, prompt injection, untrusted dependencies, or locally launched tool servers (including MCP and language servers, which are sandboxed by default when the feature is enabled).

- Available on **Windows, macOS, and Linux** (Windows requires certain OS updates).
- Enable it globally with the `chat.agent.sandbox.enabled` setting, or toggle it per-session via **Sandboxing for terminal** in the Permissions menu.
- Works for both local sessions (enforced on your machine) and connected remote sessions (enforced on the remote host).
- Sandboxing is a protective layer, not a standalone security boundary — it does not replace endpoint security.

This mirrors the sandboxing model already available in Copilot CLI (`--sandbox`/`--no-sandbox`, `/sandbox`), giving teams a consistent security posture whether developers work from the terminal or the editor. See [Copilot Configuration Basics](../copilot-configuration-basics/) for the CLI-side sandbox settings.

## Enterprise Rollout and Managed Settings

The Copilot harness is gradually becoming the default for enterprise users with VS Code experiments enabled. To preserve existing controls during the transition, VS Code automatically translates supported legacy policies into unified managed settings at runtime where possible.

Enterprises should migrate to [unified managed settings](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started) for consistent policy management across VS Code, the Copilot app, and Copilot CLI — including requiring sandboxing (`sandbox.enabled` + `sandbox.allowBypass: false`) and attributing Agent Host sessions to users and machines via telemetry identity capture.

## Other Agent Host Improvements

A few related capabilities shipped alongside the Agent Host rollout:

- **Background shell tracking**: A **Background Shells** pill above the chat input lists commands still running in the background, with live output streaming.
- **Session grid**: Arrange multiple agent sessions in a two-dimensional grid in the Agents window to compare results or monitor long-running tasks side by side.
- **Worktree cleanup**: Run **Chat: Open Worktree Cleanup** to reclaim disk space from inactive agent session worktrees, with configurable automatic cleanup for sessions whose pull requests have merged.
- **Dev Container samples (Experimental)**: Start an agent session in a ready-to-use Dev Container (Go, .NET, Node.js, PHP, Python, Rust) without cloning a repository or installing language tools locally.
- **Cross-app session handoff**: Continue a Codex chat started in ChatGPT or Codex CLI inside VS Code's Agents window (and vice versa), including using your Copilot subscription in that same conversation.

## How This Relates to Other Copilot Surfaces

| Surface | Runtime |
|---------|---------|
| VS Code (Copilot harness) | Copilot SDK, running in the Agent Host process |
| GitHub Copilot app | Copilot SDK |
| GitHub Copilot CLI | Copilot SDK |

Because all three surfaces now share the same SDK, features like sandboxing, session filtering, and external-session awareness increasingly behave the same way regardless of which client you use to start or resume a session. If you manage policy for one, most of the unified managed settings apply consistently across all three.

## Common Questions

**Q: Do I need to opt in to the Copilot harness?**

A: In recent VS Code releases, it may already be selected by default. You can still choose a different harness from the harness picker in the chat input if your rollout stage offers a choice.

**Q: Does sandboxing in VS Code replace the Copilot CLI's sandbox?**

A: No — they're parallel implementations of the same protective model, each enforced locally to their client (VS Code's Agent Host process vs. the CLI process). Enterprises can mandate both consistently via unified managed settings.

**Q: Can I use the Agent Host with remote development?**

A: Yes. Sandboxing and session management work for both local and connected remote sessions; for remote sessions, restrictions are enforced on the remote host where Copilot runs tools and commands.

## Further Reading

- [VS Code docs: Work with the Copilot harness](https://code.visualstudio.com/docs/agents/run/agent-harnesses#copilot)
- [VS Code blog: Agent Host architecture](https://code.visualstudio.com/blogs/2026/08/26/agent-host-architecture)
- [VS Code docs: Agent sandboxing](https://code.visualstudio.com/docs/agents/run/agent-sandboxing)
- [GitHub docs: Unified managed settings for enterprise](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started)
- [VS Code October 2025 (1.141) release notes](https://code.visualstudio.com/updates/v1_141)

## Next Steps

- **Compare with the Copilot app**: [Getting Started with the GitHub Copilot app](../github-copilot-app/) — a dedicated desktop surface built on the same SDK
- **Review CLI sandboxing**: [Copilot Configuration Basics](../copilot-configuration-basics/) — sandbox flags and settings for Copilot CLI
- **Understand MCP in sandboxed sessions**: [Understanding MCP Servers](../understanding-mcp-servers/) — how locally launched MCP servers are affected by sandboxing

---
