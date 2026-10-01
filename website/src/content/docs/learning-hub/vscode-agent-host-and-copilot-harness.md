---
title: 'VS Code Agent Host and the Copilot Harness'
description: 'Learn how VS Code runs agent sessions through the Agent Host Protocol, what the Copilot harness adds, and how to use multi-folder sessions and remote delegation.'
authors:
  - GitHub Copilot Learning Hub Team
lastUpdated: 2026-10-01
estimatedReadingTime: '9 minutes'
tags:
  - vscode
  - agents
  - agent-host
  - remote-sessions
relatedArticles:
  - ./agents-and-subagents.md
  - ./github-copilot-app.md
  - ./understanding-mcp-servers.md
prerequisites:
  - Basic understanding of GitHub Copilot agents
  - VS Code 1.140 or later
---

Starting with VS Code 1.140, agent sessions in VS Code run through the **Agent Host Protocol (AHP)**, a dedicated process architecture that decouples an agent session from any single editor window. This article explains what the Agent Host is, how the new **Copilot harness** uses it, and how to use the multi-folder sessions and remote delegation features it enables.

## What Is an Agent Host?

An **agent host** is a dedicated background process that runs an agent session independently of the VS Code window that created it. Instead of a session living entirely inside one editor window's memory, the session lives in the agent host process, and VS Code windows connect to it.

This has a few practical consequences:

- **Multiple windows, one session**: You can connect to the same agent session from more than one VS Code window.
- **Durability**: A session can keep running even if the originating window is closed, as long as the host process is still running.
- **Remote hosting**: Because the host is a separate process, it can run on a different machine entirely — this is what powers [remote agent hosts](#delegate-work-to-remote-agent-hosts-experimental).

## The Copilot Harness

The **Copilot harness** is a new way of running agent sessions in VS Code that is powered by the Copilot SDK — the same SDK that powers the standalone GitHub Copilot app and Copilot CLI. Because it shares the SDK, its behavior and capabilities stay consistent across all three surfaces.

To use it, select **Copilot** from the harness picker in the chat input. Depending on your rollout, it might already be the default selection. Using the Copilot harness doesn't change how you work day to day — you still describe tasks and review changes the same way — but it runs in the dedicated agent host process described above.

> Learn more about [working with agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses#use-the-copilot-harness) and the underlying [agent host architecture](https://code.visualstudio.com/blogs/2026/08/26/agent-host-architecture).

## HydraFusion Model Orchestration (Research Preview)

[HydraFusion](https://github.blog/ai-and-ml/github-copilot/project-hydrafusion-frontier-quality-via-multi-model-orchestration/) is an adaptive model orchestration system available in the model picker for eligible users with preview features enabled. Rather than you choosing a single model for a task, HydraFusion chooses the models and workflow per task:

- It can solve a task with one model.
- It can escalate to a stronger model when a task looks harder than expected.
- It can have a second model critique and revise the first model's result.

The goal is to improve result quality while balancing speed and cost, without you having to manually coordinate multiple models the way you might with a custom multi-model subagent setup.

## Multi-Folder Sessions (Experimental)

Previously, every chat in a multi-chat session shared the same folder and checkout — if you wanted to work across two repositories, you needed two separate sessions. **Multi-folder sessions** let each chat in a session use its own folder or worktree, without changes leaking between chats.

Each chat uses its folder for its terminal, tasks, changes, pull request, and Agent merge state. Chats that share a folder share that state. Hovering a session in the sessions list shows a summary across all its folders; hovering a nested chat shows details for just that chat's folder.

Example use cases:

- **Implement a feature across repositories**: Ask the main chat to work in one repository, then create a peer chat for a second repository.
- **Compare approaches in separate worktrees**: Ask the main chat to create peer chats that each use a fresh worktree of the same repository, so each approach gets its own branch, changes, and pull request.

Multi-folder sessions are off by default and have no dedicated Settings editor UI yet. Enable them in your user-scoped `settings.json`, per harness:

```json
{
  "chat.agentHost.copilotAgent.multiRootEnabled": true,
  "chat.agentHost.claudeAgent.multiRootEnabled": true,
  "chat.agentHost.codexAgent.multiRootEnabled": true
}
```

New sessions pick up the setting without an Agent Host restart. There is no UI for adding a folder or choosing a peer chat's folder yet — ask the main chat to create the peer chat and describe the repository or worktree it should use.

## Delegate Work to Remote Agent Hosts (Experimental)

Because the agent host is a separate process, your agent can delegate work to **remote agent hosts** — other machines running a connected agent host — directly from the Agents window, without you manually selecting a host in a picker for each task.

Enable this with two settings:

```json
{
  "chat.remoteAgentHostsEnabled": true,
  "chat.remoteSessions.tools.enabled": true
}
```

Once enabled and you've connected the hosts you want to use, your agent gains access to built-in tools:

| Tool | Purpose |
|------|---------|
| `list_agent_hosts` | Discover connected hosts, their models, resource capacities, and current session load |
| `create_remote_session` | Start a session on a specific host, or let automatic placement match OS, memory, and CPU requirements |
| `get_remote_session` | Check a remote session's status and latest response |
| `send_remote_message` | Send follow-up work or report results/questions back to the originating chat |

Example prompt from a chat running on an agent host:

```prompt
Start a remote session on a connected Linux host with at least 16 GiB of memory and eight logical CPUs. Check the installed Node.js and Python versions and report back to this chat.
```

A few important behaviors:

- Remote sessions have **no workspace** unless you specify one. For repository work, point the agent at an existing trusted folder on the target host (optionally in a new Git worktree).
- The tools do **not** clone or copy the originating workspace — normal folder trust and approval rules still apply on the target host.
- Keep the coordinating Agents window open and connected for messages to flow both ways; a remote agent's final answer is only delivered if it calls `send_remote_message` — it is not automatically forwarded.

## How This Relates to Subagents

The Agent Host's remote delegation tools are a different mechanism from the in-process [subagent delegation](../agents-and-subagents/) you may already use (the `agent` tool, `/fleet` in Copilot CLI, and similar). Subagents run inside the same session and share its context; remote agent host sessions are independent sessions on potentially different machines that communicate by explicit message-passing. Use subagents for tightly-coupled, same-session delegation, and remote agent hosts when you need genuinely separate environments, operating systems, or hardware profiles.

## Common Questions

**Do I need to change my custom agents, skills, or instructions to use the Copilot harness?**

No. The Copilot harness is a different execution path for running sessions, not a change to how you author agents, skills, or instructions. Your existing `.agent.md`, `.instructions.md`, and skill files work the same way.

**Is HydraFusion a replacement for manually picking a model?**

Not currently — it's a Research Preview opt-in available from the model picker for eligible accounts with preview features enabled, alongside manual model selection.

**Can I use multi-folder sessions without enabling the setting?**

No. Both the setting for your harness and workspace trust must be in place; the feature is experimental and has no dedicated Settings UI yet.

## Next Steps

- Read [Agents and Subagents](../agents-and-subagents/) to understand in-session delegation before layering remote agent hosts on top.
- Revisit [Getting Started with the GitHub Copilot app](../github-copilot-app/) to compare worktree-based parallel work in the desktop app with VS Code's multi-folder sessions.
- Keep the [GitHub Copilot Terminology Glossary](../github-copilot-terminology-glossary/) nearby when comparing agent host terminology across products.

## Further Reading

- [VS Code 1.140 release notes](https://code.visualstudio.com/updates/v1_140)
- [Agent Host architecture blog post](https://code.visualstudio.com/blogs/2026/08/26/agent-host-architecture)
- [Working with agent harnesses](https://code.visualstudio.com/docs/agents/run/agent-harnesses#use-the-copilot-harness)
- [HydraFusion: Frontier quality via multi-model orchestration](https://github.blog/ai-and-ml/github-copilot/project-hydrafusion-frontier-quality-via-multi-model-orchestration/)
- [Remote agent hosts documentation](https://code.visualstudio.com/docs/agents/run/remote-agent-sessions)

---
