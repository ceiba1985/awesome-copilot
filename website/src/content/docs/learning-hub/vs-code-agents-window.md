---
title: 'The VS Code Agents Window'
description: 'Learn about the VS Code Agents window — a dedicated space for managing agent sessions, automations, voice mode, and the new agent host architecture.'
authors:
  - GitHub Copilot Learning Hub Team
lastUpdated: 2026-09-14
estimatedReadingTime: '7 minutes'
tags:
  - vscode
  - agents
  - automations
  - voice-mode
prerequisites:
  - VS Code 1.137 or later
  - GitHub Copilot extension installed
relatedArticles:
  - ./using-automations-in-copilot-app.md
  - ./agents-and-subagents.md
  - ./what-are-agents-skills-instructions.md
  - ./github-copilot-app.md
---

VS Code's **Agents window** is a dedicated space, separate from the main editor, for starting and managing agent sessions. Recent VS Code releases have expanded it with scheduled automations, GitHub issue and pull request integration, quick chats that can grow into full workspace sessions, and a new underlying architecture called the **agent host**. This article covers what's new and how to try it.

## Automations (Preview)

**Setting**: `chat.automations.enabled`

Automations let you run recurring agent tasks on a schedule instead of starting them manually every time. Start from a template — catching up on repository changes, triaging issues, or finding bugs — or write your own prompt and schedule.

To try automations:

1. Enable the `chat.automations.enabled` setting.
2. Open the **Agents** window.
3. Select **Automations** in the sidebar.
4. Choose a starter template, or define your own prompt.
5. Run it on demand, or schedule it to run hourly, daily, or weekly.

Automations is in Preview and is rolling out gradually to all users. If you already use the [Copilot app's Automations feature](../using-automations-in-copilot-app/), the underlying concept is the same — a saved, scheduled agent prompt — just surfaced inside VS Code instead of the desktop app.

## GitHub Issue and Pull Request Integration

### Open GitHub issues and PR details inline (Experimental)

**Setting**: `extensions.experimental.enableAgentsWindowCapability`

When a chat conversation references work on GitHub, the Agents window can show issue and pull request details directly, without switching to a browser. Select a `github.com` issue or pull request link and its details open right in the Agents window — this works even without a workspace open for that repository. To try it, install the [GitHub Pull Requests extension](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github) in your default VS Code profile and enable the setting above.

### Attach issues and PRs as context

From the **Add Context...** menu in any chat input (Chat view, Chat editor, or Agents window), you can attach a GitHub issue or pull request. This makes the issue description, comments, or PR diff available to the agent without copying text into your prompt by hand. You can also paste a GitHub issue or pull request URL directly into the new-session input — the URL stays in your prompt and a context attachment is added automatically.

## Continue Quick Chats in a Workspace

You can start a quick chat in the Agents window without associating it with a workspace — useful for general questions or early-stage ideas. If the conversation becomes project-specific, ask the agent to attach a local folder and continue. After you confirm the workspace (and choose whether to use the folder directly or create an isolated worktree), the chat becomes a full workspace session, keeping its title, history, and current request while gaining access to project files.

> **Note**: Continuing a chat conversation in a workspace session currently requires the Copilot harness.

## Agent-Queued Messages

Agents can keep working in parallel without interrupting a chat that's already processing a request. When an agent uses the `send_message` session-management tool to contact a busy chat, VS Code queues the message and starts it once the active turn finishes successfully — whether that's a chat in the same session or a different one. Queued messages are processed in the order they were sent, making multi-chat workflows more predictable.

## Agent Host

The **agent host** lets you connect to the same agent session from multiple VS Code windows. It runs agent harnesses in a dedicated process based on the [Agent Host Protocol](https://microsoft.github.io/agent-host-protocol/) (AHP), an open protocol for connecting editors to agent backends. The agent host's Copilot agent is powered by the [Copilot SDK](https://www.npmjs.com/package/@github/copilot-sdk), aligning its behavior with the Copilot CLI, the standalone GitHub Copilot app, and other Copilot products.

This is under active development. See the [agent host documentation](https://code.visualstudio.com/docs/agents/concepts/agent-host) and the [agent host architecture blog post](https://code.visualstudio.com/blogs/2026/08/26/agent-host-architecture) for the rationale, architecture, and workflows you can try today.

## Voice Mode (Experimental)

**Settings**: `agents.voice.enabled`, `agents.voice.showTranscript`, `agents.voice.voice`

Voice Mode lets you have a natural, spoken conversation with an agent while it works. Enable `agents.voice.enabled` and select the **Voice Mode** button in the chat input to start. While the agent is speaking, start speaking or use the push-to-talk shortcut to interrupt and continue the conversation. Voice Mode is aware of your active session and can answer questions about running sessions, the selected model, and attached files, and it can start a new session on request — announcing whether a request is routed to an existing session or a new one.

Customize Voice Mode with:

- `agents.voice.showTranscript` — show the conversation transcript in the chat input
- **Chat: Dictate: Select Microphone** (Command Palette) — choose the input device for dictation and Voice Mode
- `agents.voice.voice` — select the voice used to read responses aloud
- **Voice Mode: Show Introduction** (Command Palette) — reopen the introduction to select a microphone and preview voices

> **Note**: Voice Mode is rolling out gradually and requires an eligible individual GitHub Copilot plan — it is not available with GitHub Copilot Business or Enterprise, and organizations can turn off Copilot preview features by policy.

## Related Editing and Chat Improvements

A few smaller changes ship alongside these agent features:

- **Smart diff editor layout**: Choose **Inline**, **Side by Side**, or **Automatic** diff layout consistently across regular diffs, multi-file diffs, and the Agents window **Changes** editor, from **More Actions** (`...`) > **Diff View**.
- **Binary files in multi-file diffs**: Changed binary files (such as images) now show a **Binary file changed** placeholder instead of being omitted, with an **Open Diff** action to review them.
- **GitHub links in the Markdown editor (Experimental)**: `markdown.experimental.richLinks.enabled` renders GitHub issue and pull request links with live title and state in the Markdown editor; `chat.experimental.richLinks.enabled` enables the same rendering in chat.

## Further Reading

- [VS Code release notes: Agents](https://code.visualstudio.com/updates) — official changelog covering Automations, Agent Host, and Voice Mode
- [Agent Host Protocol (AHP)](https://microsoft.github.io/agent-host-protocol/) — the open protocol behind the agent host
- [Agent host architecture blog post](https://code.visualstudio.com/blogs/2026/08/26/agent-host-architecture)
- [Automations documentation](https://code.visualstudio.com/docs/agents/run/automations)
- [Using Automations in the GitHub Copilot app](../using-automations-in-copilot-app/) — the equivalent feature in the desktop app

## Next Steps

- **Explore Automations**: [Using Automations in the GitHub Copilot app](../using-automations-in-copilot-app/) — the same automation concept in the desktop app
- **Understand Delegation**: [Agents and Subagents](../agents-and-subagents/) — learn when to launch subagents alongside these agent-window features
- **Configure Copilot**: [Copilot Configuration Basics](../copilot-configuration-basics/) — settings that apply across VS Code, CLI, and the app
